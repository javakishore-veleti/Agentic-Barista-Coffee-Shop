# EPIC-0006 — Identity & Access

**Phase:** B · **Depends on:** EPIC-0005, FEAT-0004-01 (the permission matrix) · **Blocks:** 0007–0018

## Intent

`authz-api`: two realms (staff and customer) over four possible providers, emitting one internal principal
shape. Plus the verification middleware every service and the gateway share.

## Why it is this early

Branch scope is enforced in the domain APIs using the principal this service issues. Any API built before it
gets authorization retrofitted, and retrofitted authorization is how scope bugs ship.

## Success measures

- Switching `AUTH_STAFF_PROVIDER` from `basic` to `cognito` changes no application code.
- A request whose principal lacks scope for the branch it names is refused by the API — proven by a test that
  calls the API directly, bypassing any UI.
- Local development runs on real tokens, real hashing, seeded users — one per role.

## Features

| ID | Name | Stories |
|----|------|---------|
| FEAT-0006-01 | Principal Model & Verification Middleware | 3 |
| FEAT-0006-02 | Staff Realm | 3 |
| FEAT-0006-03 | Customer Realm & Guest Migration | 3 |
| FEAT-0006-04 | Cloud Provider Adapters (Cognito, Entra, GCP) | 3 |

## Out of scope

MFA, SSO enrolment, and any customer-facing account management screens beyond what the migration path needs.
