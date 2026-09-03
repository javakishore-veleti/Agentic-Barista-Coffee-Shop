# FEAT-0012-04 — SQLite Sidecar Index for `json` Mode

**Epic:** EPIC-0012 · **Depends on:** FEAT-0012-02 · **Blocks:** `CATALOG_STORE=json`

## Description

File-backed does not mean index-free. In `json` mode the index is a SQLite file beside the corpus.

---

## STORY-0012-04-01 — Sidecar build

**As a** developer running `json` mode
**I want** an index built next to the corpus
**So that** queries never scan 100,000 files.

### Acceptance criteria

1. **Given** the indexer in `json` mode, **when** it runs, **then** it writes a SQLite database beside the
   corpus containing the item index, the availability index and the vector table.
2. **Given** the sidecar, **when** built, **then** it uses FTS5 for lexical search and a vector table for
   semantic search over the distinct set.
3. **Given** an incremental run, **when** shards change, **then** only the affected rows are rewritten.
4. **Given** a corpus with no sidecar, **when** a service starts in `json` mode, **then** it fails readiness
   with a message telling the operator to run the indexer — it does not fall back to scanning.

### Notes

Criterion 4 is deliberate. A silent fallback to scanning would appear to work in a small development corpus
and hang at full scale.

### Out of scope

Concurrent writers to the sidecar; the indexer is the single writer.

**OpenSpec change id:** `add-sqlite-sidecar-index`

---

## STORY-0012-04-02 — Sidecar-backed queries

**As** `catalog-api` in `json` mode
**I want** search and availability served from the sidecar
**So that** behaviour matches Postgres mode.

### Acceptance criteria

1. **Given** a search, **when** run in `json` mode, **then** it queries the sidecar and returns results whose
   ordering matches the Postgres implementation for the contract suite's cases.
2. **Given** an availability lookup, **when** run, **then** it uses the sidecar index rather than opening
   shards.
3. **Given** an item fetch by id, **when** run, **then** it opens exactly one shard file, using the sidecar
   only to locate it.

### Out of scope

Write paths beyond the append journal.

**OpenSpec change id:** `add-sidecar-query-paths`

---

## STORY-0012-04-03 — Index health and reindex

**As a** developer or operator
**I want** to see index health and trigger a reindex
**So that** staleness is visible and fixable.

### Acceptance criteria

1. **Given** an index health endpoint, **when** called, **then** it reports shard count, indexed count, failed
   count, last run time, staleness and the embedding triple.
2. **Given** a reindex trigger, **when** invoked by an authorised principal, **then** a run starts and its
   progress is observable.
3. **Given** a run already in progress, **when** another is triggered, **then** it is refused rather than
   running two writers.

### Notes

These are exactly the operations `ops-mcp` exposes in EPIC-0013, which is what lets the team ask about index
health from Claude Code in plain English.

### Out of scope

Scheduling; something else decides when reindexing happens.

**OpenSpec change id:** `add-index-health-and-reindex`
