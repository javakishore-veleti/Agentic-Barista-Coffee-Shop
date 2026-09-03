# FEAT-0009-02 — Tax Rate Table & Cache

**Epic:** EPIC-0009 · **Depends on:** FEAT-0007-01 · **Blocks:** FEAT-0009-03

## Description

A versioned rate table loaded into an in-process cache at startup. No external tax service, no network hop on
the ordering path.

---

## STORY-0009-02-01 — Rate table

**As a** platform
**I want** rates as effective-dated data per jurisdiction and category
**So that** 1,000 US branches can be taxed correctly and explainably.

### Acceptance criteria

1. **Given** the model, **when** inspected, **then** `tax_jurisdiction` carries id, name, state, county and
   city; `tax_rate` carries jurisdiction, category (`prepared`, `grocery`, `merchandise`), rate,
   `effective_from`, `effective_to` and `rate_version`.
2. **Given** a branch, **when** its jurisdiction is resolved, **then** it comes from `branch_profile`, not
   from parsing an address at request time.
3. **Given** the seed, **when** applied, **then** it loads plausible reference rates — for the reference
   jurisdiction, 4.75% state, 2.50% county and transit, and 1.00% prepared food, giving 8.25% on a prepared
   drink and 7.25% on merchandise — clearly labelled as illustrative seed data requiring verification.
4. **Given** overlapping effective ranges for one jurisdiction and category, **when** saved, **then** the save
   is refused naming the overlap.
5. **Given** a date with no applicable rate, **when** resolved, **then** it is an error, never a zero rate.

### Notes

Criterion 5 matters: a missing rate silently becoming 0% is a financial defect that looks correct on screen.

### Out of scope

An external tax rate provider integration.

**OpenSpec change id:** `add-tax-rate-table`

---

## STORY-0009-02-02 — In-process cache

**As a** service
**I want** rates in memory
**So that** pricing does not make a database call per line per order.

### Acceptance criteria

1. **Given** service startup, **when** it begins, **then** the full rate set is loaded into memory before
   readiness is reported.
2. **Given** rates that cannot be loaded, **when** startup runs, **then** the service **fails readiness** and
   does not serve traffic — it never starts with an empty cache.
3. **Given** a running service, **when** it polls `rate_version` and finds a change, **then** it reloads the
   set without a restart.
4. **Given** several instances, **when** a rate changes, **then** all converge within a documented poll
   interval.
5. **Given** the cache, **when** it serves a rate, **then** it returns the rate and its `rate_version`
   together.

### Notes

Failing readiness is the whole design. A pod that starts with no rates and computes zero tax produces
correct-looking orders that are wrong, and nothing alerts.

### Out of scope

A distributed cache. Per-instance is correct here — the data is small and read-only.

**OpenSpec change id:** `add-tax-rate-cache`

---

## STORY-0009-02-03 — Rate maintenance job

**As** finance
**I want** rate changes applied as a reviewable job
**So that** the table does not silently drift out of date.

### Acceptance criteria

1. **Given** an updated rate file, **when** the job runs, **then** it produces a diff report of every rate
   change by jurisdiction and category before applying anything.
2. **Given** the job, **when** applied, **then** it inserts new effective-dated rows and closes the previous
   ones rather than updating them in place.
3. **Given** applied changes, **when** complete, **then** `rate_version` is bumped once so instances reload.
4. **Given** the job, **when** run twice with the same input, **then** the second run reports no changes.

### Out of scope

Automated acquisition of rates from an authority.

**OpenSpec change id:** `add-tax-rate-maintenance-job`
