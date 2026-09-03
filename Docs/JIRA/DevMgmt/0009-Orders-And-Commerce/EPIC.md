# EPIC-0009 — Orders & Commerce

**Phase:** B · **Depends on:** EPIC-0008 · **Blocks:** EPIC-0013, EPIC-0014

## Intent

`orders-api`: a standard order-management document — header, lines, line modifiers, many discounts, many
taxes, shipping where goods ship, payments, and an append-only event log — plus a mocked POS behind a
contract-shaped adapter.

## The four rules the schema enforces

1. **Lines snapshot the item.** An order must not change when HQ edits a price two months later.
2. **Discounts are ordered and their result is stored.** 10% then $2 off is not $2 off then 10%.
3. **Taxes are many and per jurisdiction**, resolved from a versioned rate table and recorded with the
   `rate_version` used.
4. **Totals are stored, never derived on read.**

## Success measures

- A two-year-old order can be explained line by line without re-running today's pricing.
- A pod that cannot load tax rates fails readiness rather than computing zero tax.
- Submitting the same order twice with one idempotency key produces one order.

## Features

| ID | Name | Stories |
|----|------|---------|
| FEAT-0009-01 | Order Document Model & Migrations | 3 |
| FEAT-0009-02 | Tax Rate Table & Cache | 3 |
| FEAT-0009-03 | Pricing, Discounts & Submission | 3 |
| FEAT-0009-04 | Mock POS Adapter & Order Events | 3 |

## Out of scope

Real payment capture, real POS integration, refunds and returns.
