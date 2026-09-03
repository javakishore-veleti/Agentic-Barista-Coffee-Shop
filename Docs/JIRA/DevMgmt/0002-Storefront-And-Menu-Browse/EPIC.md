# EPIC-0002 — Storefront & Menu Browse

**Phase:** A — Portals first
**Depends on:** EPIC-0001
**Blocks:** EPIC-0006 (the catalog API is designed to serve these screens)

## Intent

Before a single agent exists, a customer should be able to open the site, learn where and when Brakeman's
is open, browse the real menu, customise an item, and build a cart. This is the ordinary path — and it
must be genuinely good on its own, because a chat surface that only wraps a broken storefront is a demo,
not a product.

## Why this comes before the catalog API

Building these screens against a fixture file forces the catalog's shape into the open. Every field the
grid, the detail panel and the cart actually need is a field the API must return; everything else is
speculation we avoid shipping.

## Success measures

- A customer can reach a priced, valid cart without ever opening a chat window.
- Every menu item on the real Brakeman's menu is representable in the fixture without a special case.
- Filtering by dietary need returns correct results with no client-side guessing about allergens.

## Features

| ID | Name | Stories |
|----|------|---------|
| [FEAT-0002-01](FEAT-0002-01-Home-And-Shop-Information.md) | Home & Shop Information | 3 |
| [FEAT-0002-02](FEAT-0002-02-Menu-Browse-And-Filter.md) | Menu Browse & Filter | 3 |
| [FEAT-0002-03](FEAT-0002-03-Item-Detail-And-Customisation.md) | Item Detail & Customisation | 3 |
| [FEAT-0002-04](FEAT-0002-04-Cart-And-Order-Review.md) | Cart & Order Review | 3 |

## Out of scope

Checkout and payment. The cart ends at a reviewed, priced order; submission arrives with EPIC-0007.
