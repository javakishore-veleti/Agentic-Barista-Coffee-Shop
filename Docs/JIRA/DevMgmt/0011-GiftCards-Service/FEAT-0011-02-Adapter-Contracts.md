# FEAT-0011-02 — Issuer & Payment Adapter Contracts

**Epic:** EPIC-0011 · **Depends on:** FEAT-0011-01 · **Blocks:** FEAT-0011-03

## Description

Mocked, contract-shaped. Method signatures mirror a real gift card and payments API so the mock can be
replaced without touching the agent or the portal.

---

## STORY-0011-02-01 — Issuer protocol

**As a** platform
**I want** issuance behind a protocol shaped like a real provider
**So that** swapping to a live issuer is a configuration change.

### Acceptance criteria

1. **Given** the protocol, **when** inspected, **then** it defines create card, activate with an order, load
   value, retrieve, and retrieve balance — mirroring the shape of a real gift card API.
2. **Given** a configured adapter, **when** the service starts, **then** the choice is logged and reported on
   `/readyz`.
3. **Given** an adapter error, **when** it occurs, **then** it is normalised to the shared error envelope with
   a `retryable` flag.
4. **Given** the mock adapter, **when** used, **then** it persists to our own ledger rather than to memory, so
   restarts do not lose cards.

### Out of scope

Any live provider integration or credentials.

**OpenSpec change id:** `add-giftcard-issuer-protocol`

---

## STORY-0011-02-02 — Payment protocol

**As a** platform
**I want** payment behind the same kind of protocol
**So that** the gift card purchase flow is complete without real money.

### Acceptance criteria

1. **Given** the protocol, **when** inspected, **then** it defines authorise, capture, void and refund with
   idempotency keys.
2. **Given** the mock, **when** used, **then** it can be configured to decline, time out and partially capture,
   so those paths are built.
3. **Given** any payment attempt, **when** it occurs, **then** it is recorded with its external reference and
   status, never only in logs.
4. **Given** the mock, **when** running, **then** the service refuses to start if it is configured with
   anything resembling real credentials.

### Notes

Criterion 4 is a guard against the mock quietly becoming a live integration by way of an environment variable.

### Out of scope

PCI scope, card data handling, 3-D Secure.

**OpenSpec change id:** `add-payment-adapter-protocol`

---

## STORY-0011-02-03 — Configuration and denominations

**As** head office
**I want** denominations and branch participation configurable
**So that** the purchase flow reflects a commercial decision.

### Acceptance criteria

1. **Given** configuration, **when** read, **then** it exposes standard denominations, custom-amount minimum
   and maximum, and whether group cards are enabled.
2. **Given** branch participation, **when** set, **then** it controls which denominations a branch promotes,
   within HQ-defined limits.
3. **Given** a purchase for an amount outside the configured bounds, **when** attempted, **then** it is refused
   naming the bound.
4. **Given** a configuration change, **when** applied, **then** it is effective-dated and does not alter cards
   already issued.

### Out of scope

Promotional bonus value campaigns.

**OpenSpec change id:** `add-giftcard-configuration-api`
