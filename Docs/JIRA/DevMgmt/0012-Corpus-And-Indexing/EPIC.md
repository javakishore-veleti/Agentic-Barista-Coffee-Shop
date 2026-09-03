# EPIC-0012 — Corpus & Indexing

**Phase:** B · **Depends on:** EPIC-0008 · **Blocks:** the `json` and `hybrid` store modes everywhere

## Intent

Generate the 300,000-shard corpus, dedupe it, index it incrementally, and embed the distinct set exactly once.

## The number that decides this epic

Embedding every shard row naively is roughly 40 GB of vectors. Embedding the deduplicated canonical catalog
— about fifty distinct items — is a few hundred kilobytes. **Nothing about the corpus changes between those
two outcomes except whether you hash content before you embed.** A pipeline that embeds per-shard rather than
per-distinct-item is a defect.

## Success measures

- The full corpus indexes incrementally: a second run with no changes does almost no work.
- Nothing scans shards at request time; `json` mode uses a SQLite sidecar index, not a linear read.
- The measured distinct-item count is published, not assumed.

## Features

| ID | Name | Stories |
|----|------|---------|
| FEAT-0012-01 | Shard Corpus Generator | 3 |
| FEAT-0012-02 | Incremental Indexer | 3 |
| FEAT-0012-03 | Dedupe & Embedding Pipeline | 3 |
| FEAT-0012-04 | SQLite Sidecar Index for `json` Mode | 3 |

## Out of scope

Distributed indexing. One machine, incremental, resumable.
