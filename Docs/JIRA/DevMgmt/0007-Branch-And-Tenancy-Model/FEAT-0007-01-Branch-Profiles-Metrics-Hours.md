# FEAT-0007-01 — Branch Profiles, Metrics & Hours

**Epic:** EPIC-0007 · **Depends on:** EPIC-0006 · **Blocks:** FEAT-0007-02, EPIC-0008, 0010

## Description

1,000 branches as data: identity, location, tax jurisdiction, hours, and the observed metrics that later
drive tiering.

---

## STORY-0007-01-01 — Branch profile model

**As a** platform
**I want** branches as first-class records
**So that** nothing about a branch is a constant in code.

### Acceptance criteria

1. **Given** the model, **when** inspected, **then** a branch carries id, name, street address, city, state,
   postcode, timezone, phone, parking notes, `is_rentable`, region and `jurisdiction_id`.
2. **Given** any branch, **when** fetched, **then** the response is identical in shape for every branch —
   no branch has special-case fields.
3. **Given** 1,000 branches, **when** listed, **then** the endpoint is paginated and filterable by region,
   state and tier, and never returns an unbounded list.
4. **Given** a branch, **when** searched by city, state or postcode, **then** results are returned within a
   documented latency bound against the full 1,000.
5. **Given** a branch id that does not exist, **when** requested, **then** a 404 with a stable code is
   returned — never an empty default branch.

### Notes

Criterion 5 is the API-level expression of the platform rule: **no defaulting to a flagship branch**, ever.

### Out of scope

Geospatial distance search; postcode and city matching is enough at this stage.

**OpenSpec change id:** `add-branch-profile-model`

---

## STORY-0007-01-02 — Per-branch hours

**As a** customer
**I want** to see the hours of the branch I selected
**So that** I do not arrive at a closed shop.

### Acceptance criteria

1. **Given** a branch, **when** its hours are fetched, **then** they comprise a weekly schedule plus a list of
   dated overrides, each with a label such as a holiday name.
2. **Given** a closed day, **when** represented, **then** it is explicitly closed, not an empty or zero-length
   range.
3. **Given** a date with an override, **when** hours are resolved for that date, **then** the override wins
   over the weekly schedule.
4. **Given** a branch and an instant, **when** asked, **then** the API answers whether it is open now and when
   it next opens, computed in the **branch's own timezone**.
5. **Given** branches across multiple US timezones, **when** open-now is computed, **then** the answer is
   correct for each without the caller passing a timezone.

### Notes

Criterion 5 is the one that bites: 1,000 US branches span at least four timezones, and a server computing
"open now" in UTC or its own local zone will be wrong for most of them for part of every day.

### Out of scope

Temporary closures pushed in real time.

**OpenSpec change id:** `add-branch-hours`

---

## STORY-0007-01-03 — Branch metrics

**As** head office
**I want** the observed facts about a branch recorded separately from its tier
**So that** tier assignment has an auditable input.

### Acceptance criteria

1. **Given** the metrics model, **when** inspected, **then** it holds seats, square footage, staff headcount,
   whether there is a separable private area, and `measured_at`.
2. **Given** metrics, **when** updated, **then** the previous values remain queryable, because a tier change
   must be explainable by a metric change.
3. **Given** a branch with no metrics, **when** the tier job runs, **then** it is left unassigned and reported,
   not defaulted into a tier.

### Out of scope

Automatic ingestion of metrics from a facilities system.

**OpenSpec change id:** `add-branch-metrics`
