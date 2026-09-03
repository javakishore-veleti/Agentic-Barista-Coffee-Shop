# FEAT-0008-03 — Store Implementations & Contract Suite

**Epic:** EPIC-0008 · **Depends on:** FEAT-0005-03, FEAT-0008-02 · **Blocks:** EPIC-0012 validation

## Description

`postgres`, `json` and `hybrid` for the catalog domain, and the parametrized suite proving they agree.

---

## STORY-0008-03-01 — Postgres implementation

**As a** platform
**I want** the default store to be indexed and transactional
**So that** the availability table performs at 18M rows a year.

### Acceptance criteria

1. **Given** `CATALOG_STORE=postgres`, **when** the service runs, **then** all reads, searches and writes use
   Postgres with pgvector for embeddings and a b-tree on `(branch_id, effective_date)`.
2. **Given** a menu read for one branch and date, **when** executed against a full-scale dataset, **then** it
   meets a documented latency bound and the query plan uses the index.
3. **Given** a vector search, **when** executed, **then** it searches only the distinct canonical set.

### Out of scope

Read replicas and connection pooling strategy beyond a documented default.

**OpenSpec change id:** `add-catalog-postgres-store`

---

## STORY-0008-03-02 — JSON and hybrid implementations

**As a** developer
**I want** the file-backed modes to work without scanning
**So that** `json` is a real option rather than a demo that falls over.

### Acceptance criteria

1. **Given** `CATALOG_STORE=json`, **when** an item is fetched by id, **then** exactly one shard file is opened.
2. **Given** `CATALOG_STORE=json`, **when** a search runs, **then** it uses the SQLite sidecar index and never
   walks the corpus.
3. **Given** `CATALOG_STORE=hybrid`, **when** a read occurs, **then** it is served from Postgres; **when** a
   write occurs, **then** files remain authoritative and the indexer reconciles.
4. **Given** any mode, **when** the index is stale relative to the corpus, **then** the service reports it on
   `/readyz` rather than serving silently outdated results.

### Out of scope

Writing to shard files from the API in `json` mode beyond an append journal.

**OpenSpec change id:** `add-catalog-json-hybrid-stores`

---

## STORY-0008-03-03 — Catalog contract suite

**As a** developer
**I want** all three modes proven identical
**So that** switching store modes is safe.

### Acceptance criteria

1. **Given** the suite, **when** run, **then** it executes every catalog read, search and filter case against
   each configured implementation and asserts identical results.
2. **Given** a divergence, **when** found, **then** the failure names both implementations and the case.
3. **Given** search ranking, **when** compared, **then** the suite asserts the same ordering, not merely the
   same set.
4. **Given** CI, **when** it runs, **then** all three modes are exercised.

### Notes

Ranking equality (criterion 3) is the one most likely to be quietly skipped and the one whose absence users
notice first.

### Out of scope

Performance comparison between modes.

**OpenSpec change id:** `add-catalog-contract-suite`
