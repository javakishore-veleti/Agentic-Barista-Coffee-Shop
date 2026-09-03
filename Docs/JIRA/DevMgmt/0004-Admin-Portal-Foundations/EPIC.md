# EPIC-0004 — Admin Portal Foundations

**Phase:** A — Portals first
**Depends on:** EPIC-0001
**Blocks:** EPIC-0006 (roles), EPIC-0007 (branch scope), EPIC-0017 (agent console)

## Intent

The staff side: catalog, branches, event inquiries and the gift card ledger — every screen scoped by the
role and branch scope the signed-in principal carries. Built against fixtures like the customer portal, so
the shape of `authz-api` and the branch model is settled by screens rather than by argument.

## Why role scoping is designed here and enforced later

The admin portal is where roles become concrete: HQ edits the brand-wide menu, regional sets event rate
cards, a branch manager touches only their own branches' availability. Drawing those screens first produces
the exact permission matrix `authz-api` must issue and the domain APIs must enforce.

**The UI scopes for usability; the API scopes for security.** Both, always. A screen that hides a control is
a convenience, never a control.

## Success measures

- Every screen states which role and branch scope can use it, and behaves correctly for each.
- The permission matrix produced here is the input to EPIC-0006, not a document written after it.

## Features

| ID | Name | Stories |
|----|------|---------|
| [FEAT-0004-01](FEAT-0004-01-Admin-Shell-And-Role-Scoping.md) | Admin Shell & Role Scoping | 3 |
| [FEAT-0004-02](FEAT-0004-02-Catalog-Management.md) | Catalog Management | 3 |
| [FEAT-0004-03](FEAT-0004-03-Branch-Management.md) | Branch Management | 3 |
| [FEAT-0004-04](FEAT-0004-04-Inquiries-And-Ledger.md) | Event Inquiries & Gift Card Ledger | 3 |

## Out of scope

The agent console — traces, cost, prompt versions — which arrives with EPIC-0017 when there is something to
observe.
