# FEAT-0011-01 — Ledger Model & Balance Integrity

**Epic:** EPIC-0011 · **Depends on:** EPIC-0006 · **Blocks:** the rest of EPIC-0011

## Description

A brand-wide, append-only ledger. Balance is computed from movements, never stored separately.

---

## STORY-0011-01-01 — Card and movement model

**As a** platform
**I want** cards and their movements as first-class records
**So that** every balance is explainable.

### Acceptance criteria

1. **Given** the model, **when** inspected, **then** a card carries id, external reference, status, currency,
   issuing branch, issued timestamp and owner reference, which may be a guest identifier.
2. **Given** `giftcard_movement`, **when** inspected, **then** each row carries type (issue, load, redeem,
   adjust), amount, branch where it occurred, actor, timestamp, order reference where applicable, and trace id.
3. **Given** the movement table, **when** examined, **then** it is append-only with no update or delete path
   through the API.
4. **Given** a card redeemed at a branch other than its issuing branch, **when** recorded, **then** both
   branches are present on the record.

### Notes

Cross-branch redemption is the normal case, not an edge case — a card bought in one city must work in another.
Branch is an attribute of a movement, never a partition of the balance.

### Out of scope

Physical card stock and activation codes.

**OpenSpec change id:** `add-giftcard-ledger-model`

---

## STORY-0011-01-02 — Computed balance

**As a** customer
**I want** my balance to be right
**So that** I am not refused at the counter with money on the card.

### Acceptance criteria

1. **Given** a card, **when** its balance is requested, **then** it is computed from the sum of movements —
   there is no stored balance column that could drift.
2. **Given** a balance response, **when** returned, **then** it includes the movement count and the timestamp
   of the latest movement, so a caller can detect staleness.
3. **Given** a card with many movements, **when** balance is requested, **then** it meets a documented latency
   bound, using a maintained aggregate that is **derived and verifiable**, not authoritative.
4. **Given** any aggregate, **when** a consistency check runs, **then** it recomputes from movements and
   reports any divergence.

### Notes

Criterion 3 permits an optimisation but constrains it: an aggregate may exist for speed, and the movements
remain the truth. The consistency check is what keeps that honest.

### Out of scope

Expiry and dormancy rules.

**OpenSpec change id:** `add-giftcard-balance`

---

## STORY-0011-01-03 — Concurrent redemption safety

**As a** platform
**I want** a card to be un-overdrawable
**So that** two simultaneous redemptions cannot both succeed.

### Acceptance criteria

1. **Given** two concurrent redemptions that together exceed the balance, **when** they race, **then** at most
   the affordable one succeeds and the other is refused with a specific insufficient-funds error.
2. **Given** a redemption, **when** processed, **then** it is atomic with the balance check.
3. **Given** a redemption with an idempotency key, **when** retried, **then** it does not double-redeem.
4. **Given** a partial redemption where the card covers part of an order, **when** processed, **then** the
   redeemed amount is exactly the card balance and the remainder is left to another payment.

### Notes

This is why the gift card ledger is Postgres-only regardless of `GIFTCARDS_STORE`. A file-backed store cannot
give criterion 1 at any level of effort.

### Out of scope

Distributed transactions across services.

**OpenSpec change id:** `add-redemption-concurrency-safety`
