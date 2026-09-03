# FEAT-0010-04 — Staff Approval Workflow API

**Epic:** EPIC-0010 · **Depends on:** FEAT-0010-03, FEAT-0004-04 · **Blocks:** EPIC-0015

## Description

The staff decision point. This endpoint is what later resolves the LangGraph interrupt, so its shape matters
beyond the admin portal.

---

## STORY-0010-04-01 — Inquiry queue API

**As a** branch manager
**I want** to list and open inquiries for my branches
**So that** nothing waits unanswered.

### Acceptance criteria

1. **Given** a scoped principal, **when** the queue is listed, **then** it returns inquiries for branches in
   scope only, filterable by status and sortable by event date.
2. **Given** an inquiry, **when** opened, **then** it returns every submitted field, the quote, the room
   window and the hold state.
3. **Given** two pending inquiries with overlapping room windows, **when** listed, **then** the conflict is
   flagged on both.
4. **Given** a principal without scope, **when** they request an inquiry, **then** it is refused and audited.

### Out of scope

Assignment of inquiries to individual staff.

**OpenSpec change id:** `add-inquiry-queue-api`

---

## STORY-0010-04-02 — Approve and decline

**As a** branch manager
**I want** to approve or decline with a note
**So that** the decision is recorded and the customer informed.

### Acceptance criteria

1. **Given** a pending inquiry, **when** approved, **then** its hold converts to a confirmed booking at the
   agreed price, and the decision is recorded with actor, timestamp and note.
2. **Given** approval of an inquiry whose room window now conflicts with a confirmed booking, **when**
   attempted, **then** it is refused with the conflict named.
3. **Given** a decline, **when** recorded, **then** the hold is released, the status changes, and both a
   customer-visible reason and an internal note may be captured separately.
4. **Given** any decision, **when** made, **then** it emits a durable event that an external consumer can
   subscribe to.

### Notes

Criterion 4 is the hook the LangGraph interrupt resolves against. Specifying it as a durable event rather than
a webhook means the agent can be down when the decision is made and still pick it up.

### Out of scope

Multi-step approval chains.

**OpenSpec change id:** `add-inquiry-approval-api`

---

## STORY-0010-04-03 — Booking lifecycle

**As a** branch manager
**I want** confirmed bookings to have a life after approval
**So that** cancellations and completions are recorded.

### Acceptance criteria

1. **Given** a confirmed booking, **when** cancelled by staff or customer, **then** the room window is
   released and the cancellation is recorded with actor and reason.
2. **Given** a booking whose date has passed, **when** the lifecycle job runs, **then** it is marked completed.
3. **Given** any transition, **when** it occurs, **then** it is appended to an event log, never overwritten.
4. **Given** a cancelled booking, **when** availability is recomputed, **then** the slot is immediately
   bookable.

### Out of scope

Cancellation fees and refunds.

**OpenSpec change id:** `add-booking-lifecycle`
