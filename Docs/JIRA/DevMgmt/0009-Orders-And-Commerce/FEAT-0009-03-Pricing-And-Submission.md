# FEAT-0009-03 — Pricing, Discounts & Submission

**Epic:** EPIC-0009 · **Depends on:** FEAT-0009-01, FEAT-0009-02 · **Blocks:** EPIC-0013, 0014

## Description

Turning a validated cart into a priced order document, and submitting it exactly once.

---

## STORY-0009-03-01 — Order pricing

**As a** caller
**I want** one call that returns the complete priced document
**So that** nothing downstream computes money.

### Acceptance criteria

1. **Given** a validated cart, branch and date, **when** priced, **then** the response contains every line with
   its modifiers, every discount with its sequence and applied amount, every tax line with jurisdiction and
   rate version, shipping if applicable, and all five totals.
2. **Given** the subtotal, **when** computed, **then** it comes from `catalog-api`'s `price_order()`, not from
   a second implementation in this service.
3. **Given** discounts and taxes, **when** applied, **then** discounts apply before tax, and the taxable base
   for each line reflects discounts allocated to it.
4. **Given** the same inputs, **when** priced twice, **then** the result is identical.
5. **Given** the response, **when** rendered by the `order_summary` card, **then** no arithmetic is needed to
   display it.

### Notes

Criterion 2 keeps a single pricing authority. Two services that each know how to price a latte will
eventually disagree by a cent, and that cent will take a day to find.

### Out of scope

Loyalty points and stored-value accrual.

**OpenSpec change id:** `add-order-pricing`

---

## STORY-0009-03-02 — Discount application

**As a** customer with a code
**I want** discounts applied predictably
**So that** the total is explainable.

### Acceptance criteria

1. **Given** several discounts, **when** applied, **then** they are applied in `sequence` and each applied
   amount is stored.
2. **Given** an order-level discount, **when** applied, **then** it is allocated across lines by a documented
   rule so that per-line tax bases are correct.
3. **Given** a discount that would take a line below zero, **when** applied, **then** it is capped and the cap
   is recorded rather than producing a negative line.
4. **Given** an invalid or expired code, **when** submitted, **then** it is refused with a specific reason.

### Out of scope

Campaign management, stacking rules and eligibility conditions beyond validity dates.

**OpenSpec change id:** `add-discount-application`

---

## STORY-0009-03-03 — Idempotent submission

**As a** caller — portal or agent
**I want** submission to be safe to retry
**So that** a dropped connection does not produce two coffees.

### Acceptance criteria

1. **Given** a submission with an idempotency key, **when** repeated with the same key and body, **then** the
   original order is returned and no second order is created.
2. **Given** the same key with a **different** body, **when** submitted, **then** it is refused as a conflict.
3. **Given** a submission, **when** it succeeds, **then** totals are written as stored values and an
   `order_event` records the transition with the actor.
4. **Given** a submission for an item that became unavailable between pricing and submission, **when**
   attempted, **then** it is refused with that line named, and no partial order is created.
5. **Given** a guest principal, **when** they submit, **then** the order is created and associated with the
   guest identifier so migration can later claim it.

### Out of scope

Payment capture; the mocked payment adapter records intent only.

**OpenSpec change id:** `add-idempotent-order-submission`
