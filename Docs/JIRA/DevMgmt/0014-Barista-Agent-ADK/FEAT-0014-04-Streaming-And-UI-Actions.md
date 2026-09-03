# FEAT-0014-04 — Streaming & `ui_action` Emission

**Epic:** EPIC-0014 · **Depends on:** FEAT-0014-01, FEAT-0003-01 · **Blocks:** nothing

## Description

Speaking the envelope the portal already renders. If this feature is hard, the envelope leaked somewhere.

---

## STORY-0014-04-01 — Envelope conformance

**As a** portal
**I want** the barista to stream exactly the envelope the mock did
**So that** switching transport changes nothing in the front end.

### Acceptance criteria

1. **Given** a turn, **when** streamed, **then** it emits `token`, `tool_call`, `tool_result`, `ui_action`,
   `error` and `done` events conforming to the published schema.
2. **Given** the schema tests from `FEAT-0003-01`, **when** run against this agent, **then** they pass
   unchanged.
3. **Given** a `done` event, **when** emitted, **then** it carries real token counts and a cost figure.
4. **Given** a provider error, **when** it occurs, **then** it is emitted as an `error` event with a stable
   code and a correct `retryable` flag — the stream is not simply dropped.

### Out of scope

Envelope changes. If one seems necessary, it is a change to `FEAT-0003-01`, versioned.

**OpenSpec change id:** `add-barista-envelope-conformance`

---

## STORY-0014-04-02 — Order summary emission

**As a** customer
**I want** my order shown as a card
**So that** I can check it properly.

### Acceptance criteria

1. **Given** a cart change or an order review request, **when** it occurs, **then** the agent emits a
   `ui_action` of kind `order_summary` carrying the priced document from `price_order`.
2. **Given** the payload, **when** emitted, **then** every figure comes from the tool result and the agent
   performs no arithmetic.
3. **Given** the emitted card, **when** rendered by the existing renderer, **then** it displays without
   modification to the renderer.
4. **Given** a response containing a monetary figure not present in that turn's tool results, **when** the
   guardrail runs, **then** the response is blocked.

### Notes

Criterion 4 is the same money-safety rule as the gift card agent, applied here. It is cheap and it is the
difference between a demo and something a shop could use.

### Out of scope

Interactive editing inside the card.

**OpenSpec change id:** `add-barista-order-summary-emission`

---

## STORY-0014-04-03 — Order status in conversation

**As a** customer
**I want** to ask where my order is
**So that** I know when to collect it.

### Acceptance criteria

1. **Given** a submitted order, **when** the customer asks about it, **then** the agent reads the order status
   stream and reports the current state.
2. **Given** the state, **when** reported, **then** it matches what the portal shows, because both read one
   source.
3. **Given** an order belonging to another principal, **when** asked about, **then** the tool refuses and the
   agent explains rather than inventing a status.

### Out of scope

Proactive notification when an order becomes ready.

**OpenSpec change id:** `add-barista-order-status`
