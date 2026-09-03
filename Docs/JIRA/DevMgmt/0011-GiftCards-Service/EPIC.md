# EPIC-0011 — Gift Cards Service

**Phase:** B · **Depends on:** EPIC-0006 · **Blocks:** EPIC-0013, EPIC-0016

## Intent

`giftcards-api`: a brand-wide ledger, denomination and participation configuration, scheduled delivery, and
issuance behind adapters shaped like a real gift card and payments API.

## Two rules

1. **The ledger is global.** A card bought at the reference branch redeems in Charlotte. Branch is an attribute of a
   movement, never a partition of the balance.
2. **Balance is the sum of movements**, computed, never a separately stored figure that can drift.

## Success measures

- `MockSquareIssuer` can be replaced by a real issuer without touching the agent or the portal.
- Concurrent redemptions of the same card cannot overdraw it.
- Every figure the gift card agent states traces to a ledger read in the same turn.

## Features

| ID | Name | Stories |
|----|------|---------|
| FEAT-0011-01 | Ledger Model & Balance Integrity | 3 |
| FEAT-0011-02 | Issuer & Payment Adapter Contracts | 3 |
| FEAT-0011-03 | Purchase, Reload & Redemption | 3 |
| FEAT-0011-04 | Delivery Scheduling & Group Cards | 3 |

## Out of scope

Real payment capture, physical cards, fraud scoring.
