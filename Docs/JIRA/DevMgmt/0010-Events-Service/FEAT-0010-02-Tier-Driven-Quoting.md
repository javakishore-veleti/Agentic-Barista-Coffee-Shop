# FEAT-0010-02 — Tier-Driven Quoting

**Epic:** EPIC-0010 · **Depends on:** FEAT-0010-01, FEAT-0007-02 · **Blocks:** EPIC-0015

## Description

Price from the tier's rate card, never from a constant and never from a model.

---

## STORY-0010-02-01 — Quote generation

**As a** customer
**I want** a quote for a specific date, time and duration
**So that** I know the price before committing.

### Acceptance criteria

1. **Given** a branch, date, start time and duration, **when** quoted, **then** the price comes from the rate
   card for that branch's tier, day type and duration, effective on that date.
2. **Given** a quote, **when** returned, **then** it states branch, event window, **room window**, price,
   guest capacity, what is included, and the tier basis.
3. **Given** a duration with no rate card row for that tier, **when** quoted, **then** the API says that
   duration is unavailable rather than interpolating.
4. **Given** the same inputs, **when** quoted twice, **then** the price is identical.
5. **Given** a quote, **when** issued, **then** it carries an expiry and a quote id.

### Notes

"What is included" is real content, from the shop's own terms: exclusive use of the space, customisable
seating, the full drinks menu, pastries by pre-order, staff on hand, and outside food permitted.

### Out of scope

Add-on pricing for catering or equipment.

**OpenSpec change id:** `add-event-quote-generation`

---

## STORY-0010-02-02 — Event type eligibility

**As a** platform
**I want** the tier to constrain what a branch may host
**So that** a small branch is not offered for an event it cannot run.

### Acceptance criteria

1. **Given** a requested event type, **when** quoted, **then** it is checked against the event types the
   branch's tier may host.
2. **Given** an ineligible type, **when** requested, **then** the response states that this branch cannot host
   it and why, rather than returning no availability.
3. **Given** eligibility rules, **when** changed in the tier table, **then** they take effect without a deploy.

### Out of scope

Per-branch exceptions to tier eligibility; a manual tier pin covers the real cases.

**OpenSpec change id:** `add-event-type-eligibility`

---

## STORY-0010-02-03 — Quote expiry and re-quoting

**As a** customer returning to an old conversation
**I want** a stale quote to be obviously stale
**So that** I am not shown a price we can no longer honour.

### Acceptance criteria

1. **Given** a quote past its expiry, **when** fetched, **then** it is returned marked expired with its
   original figures, not silently repriced.
2. **Given** an expired quote, **when** re-quoted, **then** a new quote is produced against current rate cards
   and availability, and both are linked.
3. **Given** a rate card change between quote and booking, **when** a confirmed booking exists, **then** it
   keeps its agreed price.

### Notes

This is what lets a LangGraph conversation resume days later and behave honestly — it shows what it quoted,
says it has lapsed, and offers to re-quote.

### Out of scope

Automatic price-hold guarantees.

**OpenSpec change id:** `add-quote-expiry`
