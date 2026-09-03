# FEAT-0007-02 — Tier Bands & Assignment Job

**Epic:** EPIC-0007 · **Depends on:** FEAT-0007-01, FEAT-0004-03 · **Blocks:** EPIC-0010

## Description

Tiering as data. The bands, the nightly assignment, manual pinning, and the rate cards the tier drives.

---

## STORY-0007-02-01 — Tier bands as data

**As** head office
**I want** tier definitions in a table
**So that** changing the events pricing model is not a deploy.

### Acceptance criteria

1. **Given** the model, **when** inspected, **then** a tier carries id, name, seat range, minimum square
   footage, maximum event guests, staffing cost index, sort order, and the event types it may host.
2. **Given** tier bands, **when** edited, **then** no code change or restart is required for the new bands to
   apply on the next assignment run.
3. **Given** overlapping or gapped seat ranges, **when** saved, **then** the save is refused with the specific
   overlap or gap named.
4. **Given** a proposed band change, **when** previewed, **then** the API reports how many branches would
   change tier, because that change reprices their events.

### Notes

Four tiers is the starting assumption, not a constraint — the table is meant to prove or disprove it. The
seat thresholds themselves remain a product call.

### Out of scope

Multi-dimensional tiering beyond seats, square footage and staffing index.

**OpenSpec change id:** `add-tier-bands`

---

## STORY-0007-02-02 — Assignment job

**As** head office
**I want** tiers derived from metrics automatically
**So that** 1,000 branches stay correctly tiered without manual work.

### Acceptance criteria

1. **Given** the nightly job, **when** it runs, **then** each branch with metrics is assigned a tier from the
   bands, recorded with `tier_assigned_by = auto` and a timestamp.
2. **Given** seats falling in a band and square footage disagreeing, **when** resolved, **then** seats wins and
   square footage breaks ties only within a band, per the documented rule.
3. **Given** a branch pinned with `tier_assigned_by = manual`, **when** the job runs, **then** it is skipped
   and its pin and reason are preserved.
4. **Given** a run, **when** it completes, **then** it emits a diff report of every tier change with the metric
   that caused it.
5. **Given** the job, **when** run twice with no metric changes, **then** the second run changes nothing.

### Notes

`staffing_cost_index` modifies **price**, never capacity. Conflating them produces a large branch that cannot
host a large event because it is expensive to staff, which is not the intent.

### Out of scope

Scheduling infrastructure; the job is invocable and something else decides when.

**OpenSpec change id:** `add-tier-assignment-job`

---

## STORY-0007-02-03 — Rate cards

**As a** regional manager
**I want** the rate card my tier drives
**So that** event pricing is derived, not copied 1,000 times.

### Acceptance criteria

1. **Given** the model, **when** inspected, **then** a rate card row carries tier, day type (weeknight or
   Sunday), duration in hours, price, setup minutes, cleanup minutes and `effective_from`.
2. **Given** the mid tier, **when** seeded, **then** it holds the reference figures — weeknight 2h $300,
   Sunday 2h $400, Sunday 3h $500, 60 minutes setup, 30 minutes cleanup — as ordinary editable rows.
3. **Given** a branch and a requested day and duration, **when** a rate is resolved, **then** it comes from the
   card for that branch's tier, effective on that date.
4. **Given** a rate card edit, **when** saved, **then** it is effective-dated and confirmed bookings keep the
   price agreed at the time.
5. **Given** a tier with no card for a requested duration, **when** asked, **then** the API says that duration
   is unavailable rather than interpolating a price.

### Out of scope

Demand-based or seasonal pricing.

**OpenSpec change id:** `add-event-rate-card-model`
