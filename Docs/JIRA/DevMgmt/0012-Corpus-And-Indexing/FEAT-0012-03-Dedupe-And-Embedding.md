# FEAT-0012-03 — Dedupe & Embedding Pipeline

**Epic:** EPIC-0012 · **Depends on:** FEAT-0012-02, FEAT-0005-02 · **Blocks:** catalog search at scale

## Description

Hash before you embed. This feature is the difference between a few hundred kilobytes of vectors and forty
gigabytes.

---

## STORY-0012-03-01 — Content-hash dedupe

**As** the indexer
**I want** to embed distinct content once
**So that** a thousand identical menus cost one menu.

### Acceptance criteria

1. **Given** item content, **when** hashed, **then** the hash covers exactly the fields that affect meaning —
   name and description — and excludes branch, date and availability.
2. **Given** many items sharing a hash, **when** embedded, **then** one vector is produced and all item ids map
   to it.
3. **Given** the pipeline, **when** it runs, **then** it reports distinct count, total count and the resulting
   ratio.
4. **Given** a pipeline that would embed per shard rather than per distinct hash, **when** tested, **then** the
   test fails — this is asserted, not assumed.

### Notes

Criterion 4 exists because this defect is invisible: a per-shard pipeline produces correct search results and
an enormous bill. Only a test catches it.

### Out of scope

Near-duplicate detection; exact content hashing is sufficient for a brand-wide menu.

**OpenSpec change id:** `add-content-hash-dedupe`

---

## STORY-0012-03-02 — Embedding generation

**As** the indexer
**I want** to generate and store vectors with their provenance
**So that** spaces can never be mixed.

### Acceptance criteria

1. **Given** the distinct set, **when** embedded, **then** vectors are written with the `(provider, model, dim)`
   triple recorded.
2. **Given** an existing index built with a different triple, **when** a run starts, **then** it refuses rather
   than appending incompatible vectors.
3. **Given** embedding, **when** it runs, **then** it batches requests and reports progress and cost estimate.
4. **Given** a transient provider failure, **when** it occurs, **then** the run retries with backoff and
   resumes from the last completed batch.

### Out of scope

Fine-tuning and custom embedding models.

**OpenSpec change id:** `add-embedding-generation`

---

## STORY-0012-03-03 — Re-embedding migration

**As an** operator changing embedding provider
**I want** a controlled migration
**So that** search is never half-migrated.

### Acceptance criteria

1. **Given** a new triple, **when** the migration runs, **then** it builds the new index **alongside** the old,
   with a progress table.
2. **Given** the migration in progress, **when** searches occur, **then** they continue to use the old index
   until the new one is complete.
3. **Given** a completed migration, **when** the cutover is performed, **then** it is atomic and reversible
   until the old index is dropped explicitly.
4. **Given** the job, **when** interrupted, **then** it resumes without duplicating work.

### Notes

Under the $1 posture this should rarely run, because the embedder is a self-hosted constant. The job exists so
that the day it is needed, the answer is not "rebuild everything and hope".

### Out of scope

Online dual-write during migration.

**OpenSpec change id:** `add-reembedding-migration`
