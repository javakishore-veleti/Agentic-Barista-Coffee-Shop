# FEAT-0008-04 — Deterministic Pricing & Validation

**Epic:** EPIC-0008 · **Depends on:** FEAT-0008-02, FEAT-0007-03 · **Blocks:** EPIC-0009, 0013, 0014

## Description

`price_order()` — the single authority on what a cart costs. Nothing else in the platform computes a price,
and no model ever does.

---

## STORY-0008-04-01 — Cart validation

**As a** caller
**I want** an invalid cart rejected with reasons
**So that** neither the portal nor an agent can submit something incoherent.

### Acceptance criteria

1. **Given** a cart, **when** validated, **then** every line's item, size, temperature and modifiers are
   checked against the catalog and that branch's availability.
2. **Given** an invalid combination — a modifier not valid for the chosen size — **when** validated, **then**
   the specific line and reason are returned, not a generic failure.
3. **Given** a required choice left unmade, such as temperature on a hot-or-iced item, **when** validated,
   **then** it is reported as missing rather than defaulted.
4. **Given** an item unavailable at that branch today, **when** validated, **then** that line is flagged
   unavailable and the rest of the cart still validates.

### Notes

Criterion 3 keeps the API consistent with the UI rule from `STORY-0002-03-02`: temperature is never silently
defaulted, because half the drink menu is hot-or-iced.

### Out of scope

Quantity limits driven by inventory.

**OpenSpec change id:** `add-cart-validation`

---

## STORY-0008-04-02 — Deterministic pricing

**As** the platform
**I want** one authoritative pricing function
**So that** the portal, the agent and the order service can never disagree.

### Acceptance criteria

1. **Given** a valid cart, branch and date, **when** priced, **then** the response contains per line: resolved
   unit price and its source, each modifier delta, quantity and line subtotal — plus the cart subtotal.
2. **Given** the same inputs, **when** priced twice, **then** the result is byte-identical.
3. **Given** an effective price override, **when** pricing, **then** it is applied and named in the response.
4. **Given** pricing, **when** performed, **then** it is pure and side-effect-free — pricing a cart creates
   nothing.
5. **Given** any caller, **when** it needs a total, **then** it calls this function; no other component
   multiplies a price by a quantity.

### Notes

Criterion 5 is the rule that later stops the barista agent from doing arithmetic. It only holds if the
function is genuinely convenient to call — make it so.

### Out of scope

Taxes, discounts, shipping and totals — those are order-level and live in EPIC-0009. This returns a subtotal.

**OpenSpec change id:** `add-deterministic-pricing`

---

## STORY-0008-04-03 — Pricing explainability

**As a** support agent or a curious customer
**I want** a price to be explainable
**So that** "why is this $6.25" has an answer.

### Acceptance criteria

1. **Given** a priced line, **when** inspected, **then** it states the base price, each applied delta with its
   modifier, and any override with its identifier.
2. **Given** a price differing from the brand base, **when** returned, **then** the reason is present in the
   response rather than requiring a second lookup.
3. **Given** the explanation structure, **when** rendered by the `order_summary` card, **then** no additional
   computation is needed to display it.

### Out of scope

A customer-facing price history view.

**OpenSpec change id:** `add-pricing-explainability`
