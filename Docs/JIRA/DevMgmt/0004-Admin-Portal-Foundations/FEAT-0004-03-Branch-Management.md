# FEAT-0004-03 — Branch Management

**Epic:** EPIC-0004 · **Depends on:** FEAT-0004-01 · **Blocks:** EPIC-0007

## Description

The screens for 1,000 branches: their facts, their tier, and the event rate cards the tier drives.

---

## STORY-0004-03-01 — Branch directory and profile

**As** head office
**I want** to find and inspect any branch
**So that** I can see its facts, hours and current tier in one place.

### Acceptance criteria

1. **Given** 1,000 branches, **when** the directory loads, **then** it is searchable by name, city, state and
   postcode and filterable by tier and region, paginated rather than fully rendered.
2. **Given** a branch profile, **when** it loads, **then** it shows address, hours including seasonal
   overrides, parking notes, tax jurisdiction, whether it is rentable, and its current tier with how that tier
   was assigned.
3. **Given** a branch profile, **when** edited by a principal with scope, **then** changes are versioned with
   actor and timestamp.
4. **Given** hours, **when** edited, **then** the weekly schedule and the dated override list are edited
   separately, and a closed day is expressed as closed rather than an empty range.

### Out of scope

Opening and closing branches as a lifecycle; geospatial mapping.

**OpenSpec change id:** `add-branch-directory`

---

## STORY-0004-03-02 — Tier bands and assignment

**As** head office
**I want** tier definitions and assignments to be editable data
**So that** changing the events pricing model is not a deploy.

### Acceptance criteria

1. **Given** the tier screen, **when** it loads, **then** tier bands — seat range, square footage, maximum
   event guests, staffing cost index, hostable event types — are visible and editable by HQ.
2. **Given** branch metrics, **when** the nightly assignment job runs, **then** each branch's tier is derived
   from its metrics and recorded with `tier_assigned_by = auto`.
3. **Given** a branch pinned manually with a reason, **when** the job runs, **then** it leaves that branch
   alone and the pin and its reason remain visible.
4. **Given** a change to tier bands, **when** saved, **then** the number of branches that would change tier is
   shown before confirmation, because that change reprices their events.

### Notes

Four tiers is the starting assumption; the table is meant to prove or disprove it. Seat thresholds are a
product call and the one remaining open item in the plan.

### Out of scope

Automatic collection of branch metrics from an external system.

**OpenSpec change id:** `add-branch-tier-management`

---

## STORY-0004-03-03 — Event rate cards

**As a** regional manager
**I want** to see and adjust the rate card my tier drives
**So that** event pricing reflects what a branch can actually staff.

### Acceptance criteria

1. **Given** the rate card screen, **when** it loads, **then** it shows price by tier, day type (weeknight or
   Sunday) and duration, with setup and cleanup minutes.
2. **Given** the mid tier, **when** it loads, **then** it is seeded with the Matthews figures — weeknight 2h
   $300, Sunday 2h $400, Sunday 3h $500, 60 minutes setup, 30 minutes cleanup — as editable rows.
3. **Given** an edit, **when** saved, **then** it is effective-dated and existing confirmed bookings keep their
   agreed price.
4. **Given** a rate card, **when** viewed, **then** which branches it currently applies to is shown.

### Out of scope

Seasonal or demand-based pricing.

**OpenSpec change id:** `add-event-rate-cards`
