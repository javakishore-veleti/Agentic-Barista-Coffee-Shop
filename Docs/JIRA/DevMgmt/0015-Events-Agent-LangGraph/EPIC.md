# EPIC-0015 — Events Agent (LangGraph)

**Phase:** C · **Depends on:** EPIC-0013, FEAT-0004-04 · **Blocks:** nothing

## Intent

A checkpointed booking workflow: collect requirements → check availability → quote → confirm → **interrupt
for staff approval** → hold → submit.

## Why LangGraph

The interrupt is the point. A half-finished booking must survive a page refresh **and** a staff decision that
may take a day. That is a checkpointed graph with an explicit human-in-the-loop node, not a chat loop.

## The slice worth building early

Staff approving an inquiry in the admin portal resolves the interrupt, and the customer's conversation —
reopened days later from its checkpoint — continues where it paused. This is the clearest demonstration of
why this domain got a graph. Build it early rather than as a stretch goal.

## Success measures

- Killing the service mid-booking and restarting loses nothing.
- A quote always states the full room window including setup and cleanup.
- The agent never asserts availability that `events-api` did not return.

## Features

| ID | Name | Stories |
|----|------|---------|
| FEAT-0015-01 | Booking Graph & Nodes | 3 |
| FEAT-0015-02 | Postgres Checkpointing & Resume | 3 |
| FEAT-0015-03 | Human-in-the-Loop Interrupt | 3 |
| FEAT-0015-04 | Streaming & Quote Cards | 3 |

## Out of scope

Contracts, deposits, payment for events.
