# EPIC-0008 — Catalog Service

**Phase:** B · **Depends on:** EPIC-0007 · **Blocks:** EPIC-0013, EPIC-0014

## Intent

`catalog-api`: the brand-wide canonical menu, per-branch availability, semantic and lexical search, and
**deterministic pricing**. The service that makes "grounded in the catalog" mean something.

## The shape that matters

Two levels, never one. `catalog_items` is ~50 canonical rows embedded exactly once; `branch_availability` is
the per-branch, per-date overlay. **Semantic search runs brand-wide; availability filtering runs per branch,
relationally.** A design that embeds per branch has misunderstood the domain.

## Success measures

- `search_menu(query, branch_id)` returns only what that branch has, today.
- `price_order()` is the single authority on money; the same cart priced twice gives the same answer.
- The parametrized contract suite passes identically against `json`, `postgres` and `hybrid`.

## Features

| ID | Name | Stories |
|----|------|---------|
| FEAT-0008-01 | Domain Model & Migrations | 3 |
| FEAT-0008-02 | Read, Search & Availability API | 4 |
| FEAT-0008-03 | Store Implementations & Contract Suite | 3 |
| FEAT-0008-04 | Deterministic Pricing & Validation | 3 |

## Out of scope

Inventory depletion, supplier data, nutrition information.
