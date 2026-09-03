# FEAT-0010-03 — Holds & Inquiry Submission

**Epic:** EPIC-0010 · **Depends on:** FEAT-0010-02 · **Blocks:** FEAT-0010-04, EPIC-0015

## Description

Reserving a room window while a booking is discussed, and submitting the inquiry staff will act on.

---

## STORY-0010-03-01 — Holds

**As a** customer mid-conversation
**I want** the slot reserved while we agree details
**So that** it is not taken while I am typing.

### Acceptance criteria

1. **Given** an available slot, **when** a hold is placed, **then** the **room window** is reserved for a
   configurable TTL and excluded from availability.
2. **Given** an expired hold, **when** availability is computed, **then** it is ignored and the slot is
   bookable again.
3. **Given** two concurrent hold attempts on overlapping room windows, **when** they race, **then** exactly one
   succeeds and the other receives a specific conflict error.
4. **Given** a hold, **when** it is released or converted to a booking, **then** the transition is recorded.
5. **Given** a hold, **when** the holder returns before expiry, **then** it can be extended once within a
   documented limit.

### Notes

Criterion 3 requires a real transactional guarantee, which is why event holds are Postgres-only regardless of
`EVENTS_STORE`.

### Out of scope

Queueing or waitlisting on a held slot.

**OpenSpec change id:** `add-event-holds`

---

## STORY-0010-03-02 — Inquiry submission

**As a** customer
**I want** to submit my event request with all the details
**So that** staff can act on it without a round trip.

### Acceptance criteria

1. **Given** a submission, **when** validated, **then** it requires first name, last name, email, phone,
   event date, alternate date, event type, desired start and length, guest count and bar type.
2. **Given** a missing required field, **when** submitted, **then** the specific field is named.
3. **Given** a valid submission, **when** accepted, **then** it is linked to its quote and hold, given a
   reference, and set to pending.
4. **Given** a submission whose hold has expired, **when** attempted, **then** it is refused with a
   re-quote suggestion rather than silently booking an unheld slot.
5. **Given** a submission, **when** stored, **then** the alternate date is retained, because staff use it when
   declining the first.

### Out of scope

Contracts, deposits and signatures.

**OpenSpec change id:** `add-event-inquiry-submission`

---

## STORY-0010-03-03 — Customer-visible status

**As a** customer
**I want** to see where my inquiry stands
**So that** I am not waiting without information.

### Acceptance criteria

1. **Given** an inquiry reference, **when** fetched, **then** it returns status, submitted details, the quote
   and the room window.
2. **Given** a status change by staff, **when** it occurs, **then** it is visible to the customer without a
   further action.
3. **Given** a declined inquiry, **when** viewed, **then** any staff note intended for the customer is shown,
   and internal notes are not.

### Out of scope

Email and SMS notification delivery.

**OpenSpec change id:** `add-inquiry-status-endpoint`
