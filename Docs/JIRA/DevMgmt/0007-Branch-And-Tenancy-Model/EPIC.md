# EPIC-0007 — Branch & Tenancy Model

**Phase:** B · **Depends on:** EPIC-0006, FEAT-0004-03 · **Blocks:** 0008–0012

## Intent

1,000 branches as first-class data: profiles, metrics, data-driven tiers, the rate cards tiers drive, tax
jurisdiction assignment, and the scope enforcement every other API depends on.

## The three rules this epic exists to make true

1. **Tiering is data, not an enum.** Bands live in a table; assignment is a nightly job; manual pins are
   recorded with a reason.
2. **Price authority is a policy table with an approval trail.** The first exception is a row, not an ad-hoc
   database edit.
3. **`branch_id` is required, never defaulted.** A call without one fails loudly rather than answering from a
   flagship branch.

## Success measures

- Adding a tier or moving a seat threshold requires no deploy, and shows how many branches it repricess.
- A `branch_manager` principal provably cannot read or write another branch's data through any API.

## Features

| ID | Name | Stories |
|----|------|---------|
| FEAT-0007-01 | Branch Profiles, Metrics & Hours | 3 |
| FEAT-0007-02 | Tier Bands & Assignment Job | 3 |
| FEAT-0007-03 | Price Policy & Override Workflow | 3 |
| FEAT-0007-04 | Scope Enforcement Middleware | 3 |

## Out of scope

Branch opening and closing as a lifecycle; geospatial search beyond postcode and city matching.
