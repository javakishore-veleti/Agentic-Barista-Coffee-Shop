# FEAT-0005-03 — Repository / Store Abstraction

**Epic:** EPIC-0005 · **Depends on:** FEAT-0005-01 · **Blocks:** EPIC-0008, 0010, 0011, 0012

## Description

The `Repository` protocol shape, the three implementations' common machinery, and the parametrized contract
suite that is the only thing keeping the modes from diverging.

---

## STORY-0005-03-01 — Repository protocol and resolution

**As a** developer of a domain API
**I want** a repository handed to me at startup
**So that** no endpoint branches on the store mode.

### Acceptance criteria

1. **Given** `<DOMAIN>_STORE`, **when** set to `postgres`, `json` or `hybrid`, **then** the service resolves
   the matching implementation once at startup and injects it.
2. **Given** any endpoint, **when** inspected, **then** it contains no conditional on the store mode.
3. **Given** an invalid combination — for example a transactional domain configured as `json` — **when** the
   service starts, **then** it refuses to start and names the constraint.
4. **Given** the resolved repository, **when** `/readyz` is called, **then** the response reports which
   implementation is active.

### Notes

Criterion 3 is the enforcement point for the platform rule: **reference data is pluggable, transactional data
is Postgres, always.** Orders, bookings, holds and the ledger must refuse a file-backed configuration rather
than accept one and lose writes.

### Out of scope

Domain-specific query methods; each domain defines its own.

**OpenSpec change id:** `add-repository-protocol`

---

## STORY-0005-03-02 — Shard reading machinery

**As a** developer implementing a `json` repository
**I want** shard addressing, parsing and caching provided
**So that** three domains do not each write it.

### Acceptance criteria

1. **Given** an item id of the form `<domain>:<shard>:<offset>`, **when** resolved, **then** exactly one file
   is opened and the item returned without scanning others.
2. **Given** repeated reads from one shard, **when** they occur, **then** the parsed shard is served from an
   in-process LRU with a configurable bound.
3. **Given** a corrupt or unparseable shard, **when** read, **then** the error names the file and offset and
   does not take down the service.
4. **Given** the shard manifest, **when** consulted, **then** it provides path, sha256, item count and mtime
   per shard.

### Out of scope

The indexer itself, and the SQLite sidecar; both are EPIC-0012.

**OpenSpec change id:** `add-shard-reading`

---

## STORY-0005-03-03 — Parametrized contract suite

**As a** developer
**I want** one test suite run against every repository implementation
**So that** "or a mix" cannot rot into two incompatible behaviours.

### Acceptance criteria

1. **Given** a domain's contract suite, **when** run, **then** it executes once per configured implementation
   and asserts identical results for identical inputs.
2. **Given** any divergence in ordering, pagination, filtering or absence semantics, **when** it occurs,
   **then** the suite fails naming the two implementations and the differing case.
3. **Given** a new implementation, **when** added, **then** it is included by registration, not by copying
   test files.
4. **Given** CI, **when** it runs, **then** the suite runs for every domain that has more than one
   implementation.

### Notes

This suite is the load-bearing test in the whole platform. If it is slow or flaky it will be skipped, and the
seam will silently stop being real — budget for it to stay fast.

### Out of scope

Performance benchmarking between implementations.

**OpenSpec change id:** `add-repository-contract-suite`
