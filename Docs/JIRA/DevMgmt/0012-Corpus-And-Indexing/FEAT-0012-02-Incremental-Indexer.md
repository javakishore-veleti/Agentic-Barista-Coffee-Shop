# FEAT-0012-02 — Incremental Indexer

**Epic:** EPIC-0012 · **Depends on:** FEAT-0012-01 · **Blocks:** the `json` and `hybrid` modes

## Description

The only process that bulk-reads shards. Incremental, idempotent, resumable.

---

## STORY-0012-02-01 — Shard manifest

**As** the indexer
**I want** to know what I have already processed
**So that** a second run does almost no work.

### Acceptance criteria

1. **Given** the manifest, **when** inspected, **then** each row carries shard id, path, sha256, item count,
   mtime and indexed-at timestamp.
2. **Given** an unchanged shard, **when** a run occurs, **then** its checksum matches and it is skipped.
3. **Given** a changed shard, **when** detected, **then** only that shard is reprocessed.
4. **Given** a deleted shard, **when** detected, **then** its rows are removed and the deletion recorded.
5. **Given** a run with no changes across 300,000 files, **when** it completes, **then** it does so within a
   documented bound.

### Out of scope

Watching the filesystem for changes in production; a local watcher is a development convenience.

**OpenSpec change id:** `add-shard-manifest`

---

## STORY-0012-02-02 — Indexing run

**As a** developer
**I want** the corpus indexed reliably
**So that** query paths never touch shards.

### Acceptance criteria

1. **Given** a run, **when** it processes shards, **then** it does so in parallel with a configurable
   concurrency and bounded memory.
2. **Given** a run killed mid-way, **when** restarted, **then** it resumes without reprocessing completed
   shards and without leaving partial rows.
3. **Given** a malformed shard, **when** encountered, **then** it is recorded as failed, the run continues,
   and the summary reports failures.
4. **Given** a run, **when** it completes, **then** it reports shards processed, skipped, failed, rows written
   and elapsed time.
5. **Given** an index older than the corpus, **when** a service checks readiness, **then** the staleness is
   reported.

### Out of scope

Distributed indexing across machines.

**OpenSpec change id:** `add-indexing-run`

---

## STORY-0012-02-03 — Availability index

**As** the catalog
**I want** the availability dimension indexed relationally
**So that** 18 million rows a year query in milliseconds.

### Acceptance criteria

1. **Given** indexed availability, **when** stored, **then** it is keyed by branch, item and effective date
   with a b-tree supporting lookups by branch and date.
2. **Given** a full year of data at 1,000 branches, **when** one branch and date is queried, **then** it meets
   a documented latency bound and the plan uses the index.
3. **Given** availability rows, **when** written, **then** **no embedding is generated for them** — availability
   is relational, not semantic.

### Notes

Criterion 3 is worth asserting in a test. It is the single easiest mistake to make here, and it is the one
that turns a cents-scale embedding job into a five-figure one.

### Out of scope

Partitioning strategy for multi-year retention.

**OpenSpec change id:** `add-availability-index`
