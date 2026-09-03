# FEAT-0009-01 — Order Document Model & Migrations

**Epic:** EPIC-0009 · **Depends on:** EPIC-0008 · **Blocks:** the rest of EPIC-0009

## Description

The order as a standard commerce document. Header, lines, line modifiers, many discounts, many taxes,
shipping, payments, and an append-only event log.

---

## STORY-0009-01-01 — Header, lines and modifiers

**As a** platform
**I want** orders modelled as a document, not a cart with a total
**So that** the first promotion does not break the schema.

### Acceptance criteria

1. **Given** the model, **when** inspected, **then** `order_header` carries order id, branch id, customer
   reference, channel (`web`, `chat`, `pos`), status, currency, placed and fulfil timestamps, and the five
   stored totals: subtotal, discount, tax, shipping and grand total.
2. **Given** `order_line`, **when** inspected, **then** it carries line number, item id, **item snapshot**,
   quantity, unit price, and the four line totals.
3. **Given** an item snapshot, **when** stored, **then** it holds the item's name, description and price as of
   purchase, sufficient to render the line without reading the catalog.
4. **Given** a catalog change after an order is placed, **when** the order is re-read, **then** it is
   unchanged.
5. **Given** `order_line_modifier`, **when** inspected, **then** each modifier is stored with its own snapshot
   and price delta.

### Notes

The snapshot is not denormalisation for speed — it is correctness. The menu is brand-wide and mutable, and an
order that changes when HQ edits a price is a defect that surfaces in an audit.

### Out of scope

Order amendment after submission.

**OpenSpec change id:** `add-order-document-model`

---

## STORY-0009-01-02 — Discounts, taxes and shipping

**As a** platform
**I want** many discounts and many taxes per order and per line
**So that** real promotions and real US tax are representable.

### Acceptance criteria

1. **Given** `order_discount`, **when** inspected, **then** it carries code, scope (order or line), type
   (percentage or amount), value, **sequence**, and the applied amount.
2. **Given** two discounts on one order, **when** applied, **then** they are applied in `sequence` order and
   each applied amount is stored — not recomputed on read.
3. **Given** `order_tax`, **when** inspected, **then** it carries scope, jurisdiction, rate, taxable base, tax
   amount and the `rate_version` used.
4. **Given** taxes, **when** applied, **then** they attach per line, so a prepared drink and a bag of beans in
   one order are taxed differently.
5. **Given** `order_shipping`, **when** present, **then** it carries method, address reference, cost and tax;
   **given** a drinks-only order, **then** it is absent rather than zero-valued in a second order type.

### Notes

Ten percent off then two dollars off is not two dollars off then ten percent. Storing `sequence` and the
resulting `applied_amount` is what makes a total reproducible.

### Out of scope

Discount eligibility rules and campaign management.

**OpenSpec change id:** `add-order-discounts-taxes-shipping`

---

## STORY-0009-01-03 — Payments and the event log

**As a** platform
**I want** payments and status transitions recorded append-only
**So that** an order's life is reconstructable.

### Acceptance criteria

1. **Given** `order_payment`, **when** inspected, **then** it carries method (`giftcard`, `card`, `mock`),
   amount, external reference and status, and an order may have several.
2. **Given** `order_event`, **when** inspected, **then** it is append-only, carrying the transition, actor,
   timestamp and trace id — with no update or delete path.
3. **Given** an order, **when** its events are replayed, **then** its current status is derivable from them.
4. **Given** split payment across a gift card and another method, **when** recorded, **then** both rows exist
   and sum to the grand total.

### Out of scope

Refunds, chargebacks and partial returns.

**OpenSpec change id:** `add-order-payments-and-events`
