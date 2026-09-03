# FEAT-0010-01 — Venue Calendar & Slot Arithmetic

**Epic:** EPIC-0010 · **Depends on:** EPIC-0007 · **Blocks:** the rest of EPIC-0010

## Description

The arithmetic that must never live in a prompt: which windows a branch can rent, and what a booking actually
occupies once setup and cleanup are counted.

---

## STORY-0010-01-01 — Rentable windows

**As a** platform
**I want** to know when a branch can host a private event
**So that** availability reflects the shop's actual operating pattern.

### Acceptance criteria

1. **Given** a branch marked `is_rentable`, **when** its rentable windows are computed, **then** they cover
   weeknight evenings after close and full days on which the branch is closed to the public.
2. **Given** the reference branch, **when** windows are computed, **then** weeknight evenings from 7pm and all
   day Sunday are rentable, because the branch is closed to the public on Sunday.
3. **Given** a branch whose weekly hours differ, **when** windows are computed, **then** they derive from that
   branch's hours — no pattern is hardcoded.
4. **Given** a dated hours override such as a holiday, **when** windows are computed for that date, **then**
   the override is respected.
5. **Given** a branch not marked rentable, **when** asked, **then** it returns no windows rather than an error.

### Notes

The relationship is the point: a branch is rentable *because* it is closed. Deriving windows from hours means
a branch that changes its Sunday opening automatically stops offering Sunday rentals.

### Out of scope

Blackout dates as a separate management concept; a dated hours override covers it.

**OpenSpec change id:** `add-rentable-windows`

---

## STORY-0010-01-02 — Room window arithmetic

**As a** platform
**I want** setup and cleanup counted in what a booking occupies
**So that** two events cannot be double-booked into one evening.

### Acceptance criteria

1. **Given** an event start, a duration and the tier's setup and cleanup minutes, **when** the room window is
   computed, **then** it spans from start minus setup to end plus cleanup.
2. **Given** a 2-hour event starting at 7pm with 60 minutes setup and 30 cleanup, **when** computed, **then**
   the room window is 6:00pm to 9:30pm.
3. **Given** a room window extending outside the branch's rentable window, **when** checked, **then** the
   booking is refused, naming which end overruns.
4. **Given** two bookings whose **room windows** overlap, **when** the second is attempted, **then** it is
   refused — even if the event windows themselves do not overlap.
5. **Given** all computations, **when** performed, **then** they use the branch's own timezone.

### Notes

Criterion 4 is the defect this story exists to prevent: two events an hour apart look fine on the event
windows and are impossible in the room.

### Out of scope

Variable setup time by event type.

**OpenSpec change id:** `add-room-window-arithmetic`

---

## STORY-0010-01-03 — Availability search

**As a** customer
**I want** to ask what is available
**So that** I can pick a date that works.

### Acceptance criteria

1. **Given** a branch, a date range, a duration and a guest count, **when** searched, **then** it returns the
   slots that fit — capacity, rentable window and room window all satisfied.
2. **Given** a guest count above the branch tier's maximum, **when** searched, **then** it returns no slots and
   states the capacity limit rather than an empty result.
3. **Given** existing bookings and holds, **when** searching, **then** both are excluded, and an expired hold
   is not.
4. **Given** a search, **when** it returns a slot, **then** the slot states both the event window and the room
   window.

### Out of scope

Suggesting alternative branches.

**OpenSpec change id:** `add-event-availability-search`
