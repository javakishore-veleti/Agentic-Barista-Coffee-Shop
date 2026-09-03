# FEAT-0015-04 — Streaming & Quote Cards

**Epic:** EPIC-0015 · **Depends on:** FEAT-0015-01, FEAT-0003-03 · **Blocks:** nothing

## Description

The events agent speaking the same envelope, and emitting the quote card the portal already renders.

---

## STORY-0015-04-01 — Envelope conformance

**As a** portal
**I want** the events agent to stream the published envelope
**So that** the surface switches from mock to live by configuration.

### Acceptance criteria

1. **Given** a turn, **when** streamed, **then** it conforms to the published schema and passes the
   `FEAT-0003-01` tests unchanged.
2. **Given** a graph node transition, **when** it occurs, **then** it is surfaced as a `tool_call` or progress
   indication in customer language, not as an internal node name.
3. **Given** an interrupt, **when** reached, **then** the stream completes cleanly with a `done` event — it
   does not hang waiting for a human.

### Notes

Criterion 3 matters: an interrupt is not a stalled request. The turn ends, and the conversation resumes later
as a new turn.

### Out of scope

Envelope changes.

**OpenSpec change id:** `add-events-agent-envelope-conformance`

---

## STORY-0015-04-02 — Quote card emission

**As a** customer
**I want** the quote as a card with the room window drawn
**So that** I understand what I am paying for.

### Acceptance criteria

1. **Given** a quote, **when** produced, **then** the agent emits a `ui_action` of kind `event_quote` carrying
   the tool's response verbatim.
2. **Given** the payload, **when** emitted, **then** it includes event window, room window, price, capacity,
   inclusions, tier basis and expiry.
3. **Given** an expired quote, **when** re-rendered, **then** the payload marks it stale so the renderer shows
   it as such.
4. **Given** any price in the response text, **when** the guardrail runs, **then** it must match a figure from
   that turn's tool results or the response is blocked.

### Out of scope

Calendar file generation.

**OpenSpec change id:** `add-event-quote-card-emission`

---

## STORY-0015-04-03 — Availability honesty

**As a** customer
**I want** the agent to never guess about dates
**So that** I do not plan around a slot that is not there.

### Acceptance criteria

1. **Given** any statement about availability, **when** made, **then** it traces to a `find_slots` result from
   the current turn.
2. **Given** a date not checked, **when** the customer asks, **then** the agent checks rather than reasoning
   from a previous answer.
3. **Given** a room window conflict, **when** it exists, **then** the agent explains the setup and cleanup
   requirement rather than saying the slot is simply taken.
4. **Given** a response asserting availability with no supporting tool result, **when** the guardrail runs,
   **then** it is blocked.

### Notes

Criterion 3 turns a confusing refusal into a useful one: "7pm is free but we need the room from 6 for setup"
is something a customer can work with.

### Out of scope

Proposing alternative branches.

**OpenSpec change id:** `add-events-availability-honesty`
