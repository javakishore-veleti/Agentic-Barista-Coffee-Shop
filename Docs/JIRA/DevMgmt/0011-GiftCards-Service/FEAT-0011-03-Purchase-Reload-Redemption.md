# FEAT-0011-03 — Purchase, Reload & Redemption

**Epic:** EPIC-0011 · **Depends on:** FEAT-0011-02 · **Blocks:** EPIC-0013, 0016

## Description

The three write flows, each idempotent, each producing ledger movements.

---

## STORY-0011-03-01 — Purchase

**As a** customer
**I want** to buy a gift card
**So that** I can give it to someone.

### Acceptance criteria

1. **Given** a valid amount and recipient details, **when** purchased, **then** payment is authorised, a card
   is issued, an issue movement is written, and the card reference is returned.
2. **Given** payment failure, **when** it occurs, **then** no card is issued and no movement is written.
3. **Given** card issuance failing after payment authorisation, **when** it occurs, **then** the authorisation
   is voided and the failure reported — no money is captured without a card.
4. **Given** an idempotency key, **when** the purchase is retried, **then** one card exists.
5. **Given** a guest purchaser, **when** they buy, **then** the card is associated with the guest identifier so
   later registration can claim it into a wallet.

### Notes

Criterion 3 is the ordering that matters: authorise, issue, then capture. Capturing first and failing to issue
takes money for nothing.

### Out of scope

Physical card fulfilment.

**OpenSpec change id:** `add-giftcard-purchase`

---

## STORY-0011-03-02 — Reload

**As a** customer
**I want** to add value to an existing card
**So that** I can keep using the same card.

### Acceptance criteria

1. **Given** an active card and a valid amount, **when** reloaded, **then** payment is authorised, a load
   movement is written, and the new balance is returned.
2. **Given** a cancelled or expired card, **when** reload is attempted, **then** it is refused with the reason.
3. **Given** a reload taking the card above a configured maximum, **when** attempted, **then** it is refused
   naming the limit.
4. **Given** an idempotency key, **when** retried, **then** value is added once.

### Out of scope

Auto-reload and recurring top-up.

**OpenSpec change id:** `add-giftcard-reload`

---

## STORY-0011-03-03 — Redemption against an order

**As a** customer
**I want** to pay with my gift card
**So that** the value gets used.

### Acceptance criteria

1. **Given** an order and a card with sufficient balance, **when** redeemed, **then** a redeem movement is
   written, an `order_payment` row is created, and both reference each other.
2. **Given** a card with a balance less than the order total, **when** redeemed, **then** the full card balance
   is applied and the remainder is left outstanding for another payment method.
3. **Given** an order that fails after redemption, **when** it fails, **then** the redemption is reversed by a
   compensating adjustment movement — never by deleting the original.
4. **Given** a redemption, **when** recorded, **then** the branch where it occurred is stored, which may differ
   from the issuing branch.

### Notes

Criterion 3 preserves the append-only invariant. A reversal is a new movement with a link to the one it
compensates; deleting history would make balances unexplainable.

### Out of scope

Refunds to the original payment method.

**OpenSpec change id:** `add-giftcard-redemption`
