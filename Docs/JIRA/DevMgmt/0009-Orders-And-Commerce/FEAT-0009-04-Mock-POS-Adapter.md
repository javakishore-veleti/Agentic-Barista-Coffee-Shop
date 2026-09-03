# FEAT-0009-04 — Mock POS Adapter & Order Events

**Epic:** EPIC-0009 · **Depends on:** FEAT-0009-03 · **Blocks:** EPIC-0014

## Description

Fulfilment behind a contract-shaped adapter. The same decision as payments: real contract, no real
integration.

---

## STORY-0009-04-01 — PosAdapter contract

**As a** platform
**I want** fulfilment behind a protocol shaped like a real point-of-sale
**So that** a real POS can be wired in without touching the agent or the portal.

### Acceptance criteria

1. **Given** the protocol, **when** inspected, **then** it defines submit ticket, poll status, void and close,
   with request and response types mirroring a real POS.
2. **Given** an adapter selected by configuration, **when** the service starts, **then** the choice is logged
   and reported on `/readyz`.
3. **Given** an adapter failure, **when** it occurs, **then** it is normalised to the shared error envelope
   and the order remains in a recoverable state.

### Out of scope

Any real POS vendor integration.

**OpenSpec change id:** `add-pos-adapter-contract`

---

## STORY-0009-04-02 — Mock adapter

**As a** developer
**I want** orders to progress realistically
**So that** the portal and the agent have real states to render.

### Acceptance criteria

1. **Given** a submitted order, **when** the mock adapter accepts it, **then** it transitions
   accepted → in progress → ready → collected on a configurable timer.
2. **Given** each transition, **when** it occurs, **then** an `order_event` row is written with the actor
   recorded as the adapter.
3. **Given** the mock, **when** configured to fail, **then** it can produce rejection, timeout and void, so
   those paths are built and seen.
4. **Given** a restart mid-progression, **when** the service comes back, **then** the order resumes from its
   recorded state rather than restarting the timer from the beginning.

### Out of scope

Kitchen display simulation.

**OpenSpec change id:** `add-mock-pos-adapter`

---

## STORY-0009-04-03 — Order status streaming

**As a** customer
**I want** to watch my order progress
**So that** I know when to walk up to the counter.

### Acceptance criteria

1. **Given** an order, **when** its status endpoint is subscribed to, **then** transitions are streamed as
   they are written.
2. **Given** a subscriber joining late, **when** it subscribes, **then** it receives the current state first,
   then subsequent transitions.
3. **Given** the same stream, **when** consumed by the portal and by the barista agent, **then** both render
   the same states from one source.
4. **Given** a principal without rights to an order, **when** it subscribes, **then** it is refused.

### Notes

One stream for both consumers is what stops the agent and the portal telling a customer two different things
about the same order.

### Out of scope

Push notifications and SMS.

**OpenSpec change id:** `add-order-status-streaming`
