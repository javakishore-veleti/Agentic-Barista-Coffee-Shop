# FEAT-0012-01 — Shard Corpus Generator

**Epic:** EPIC-0012 · **Depends on:** EPIC-0008 · **Blocks:** the rest of EPIC-0012

## Description

Generate 300,000 shard files that model the real domain: 1,000 branches × ~100 dated snapshots per domain.

---

## STORY-0012-01-01 — Branch and catalog shards

**As a** developer
**I want** a generated corpus that mirrors the real structure
**So that** measurements taken against it mean something.

### Acceptance criteria

1. **Given** the generator, **when** run, **then** it produces ~100,000 catalog shards as 1,000 branches ×
   ~100 dated snapshots, each holding that branch's menu state on that date.
2. **Given** any two branches' shards for the same date, **when** compared, **then** the **item definitions
   are byte-identical** — only availability, rotation and any override differ.
3. **Given** the generator, **when** run with the same seed, **then** it produces an identical corpus.
4. **Given** the corpus, **when** validated, **then** every shard passes the schema and the validation failure
   names the file and offset.
5. **Given** a scale parameter, **when** set, **then** a smaller corpus can be generated for cloud demos
   without changing structure.

### Notes

Criterion 2 is the entire reason the embedding bill is cents rather than thousands. The generator must produce
genuine duplication, not randomised text that would defeat dedupe and misrepresent the domain.

### Out of scope

Generating from real production data.

**OpenSpec change id:** `add-catalog-shard-generator`

---

## STORY-0012-01-02 — Event and gift card shards

**As a** developer
**I want** the other two corpora generated with realistic variance
**So that** the indexer is exercised where dedupe does not help.

### Acceptance criteria

1. **Given** the generator, **when** run, **then** it produces ~100,000 event shards holding branch calendars,
   capacity and rate card state, which **genuinely differ** by branch and tier.
2. **Given** the generator, **when** run, **then** it produces ~100,000 gift card configuration shards as
   brand-wide configuration crossed with branch participation.
3. **Given** the event corpus, **when** measured, **then** its duplication rate is reported and is materially
   lower than the catalog's.

### Notes

The contrast is deliberate and worth measuring: the catalog corpus proves dedupe, the event corpus proves the
indexer does real work. Reporting both rates makes the design argument concrete.

### Out of scope

Realistic booking history.

**OpenSpec change id:** `add-event-giftcard-shard-generators`

---

## STORY-0012-01-03 — Corpus statistics

**As a** developer
**I want** the corpus measured, not assumed
**So that** sizing decisions rest on numbers.

### Acceptance criteria

1. **Given** a generated corpus, **when** the statistics command runs, **then** it reports file count, total
   bytes, rows per domain, and **the distinct-item count after content hashing**.
2. **Given** the statistics, **when** produced, **then** they are written to a file committed alongside the
   plan, so the claimed numbers are checkable.
3. **Given** a regenerated corpus, **when** statistics are recomputed, **then** any material change from the
   recorded figures is reported.

### Notes

The plan asserts the distinct set is around fifty items. This story is what turns that from an assertion into
a measurement.

### Out of scope

Performance benchmarking; that is indexing, not generation.

**OpenSpec change id:** `add-corpus-statistics`
