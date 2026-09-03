# EPIC-0010 — Events Service

**Phase:** B · **Depends on:** EPIC-0007 · **Blocks:** EPIC-0013, EPIC-0015

## Intent

`events-api`: branch-tiered rate cards, correct slot arithmetic, holds, inquiry submission and the staff
approval workflow.

## The arithmetic that must not live in a prompt

A 2-hour Sunday booking starting at 7pm occupies the room from **6:00pm to 9:30pm** once 60 minutes of setup
and 30 of cleanup are counted. Availability reserves the full room window, never just the event window. The
shop is closed Sundays, which is exactly why Sundays are rentable.

## Success measures

- Two bookings whose room windows overlap can never both be held.
- A quote states the room window, not only the event window.
- Branch tier drives price, capacity and hostable event types with no branch-specific code.

## Features

| ID | Name | Stories |
|----|------|---------|
| FEAT-0010-01 | Venue Calendar & Slot Arithmetic | 3 |
| FEAT-0010-02 | Tier-Driven Quoting | 3 |
| FEAT-0010-03 | Holds & Inquiry Submission | 3 |
| FEAT-0010-04 | Staff Approval Workflow API | 3 |

## Out of scope

Contracts, deposits, invoicing, and catering pre-orders beyond flagging that pastries are pre-order only.
