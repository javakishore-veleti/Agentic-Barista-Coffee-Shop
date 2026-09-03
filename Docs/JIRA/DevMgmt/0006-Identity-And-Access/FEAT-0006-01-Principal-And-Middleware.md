# FEAT-0006-01 — Principal Model & Verification Middleware

**Epic:** EPIC-0006 · **Depends on:** EPIC-0005, FEAT-0004-01 · **Blocks:** all of 0006–0018

## Description

The internal principal shape and the middleware every service shares. This is the contract that makes four
identity providers invisible to everything downstream.

---

## STORY-0006-01-01 — The principal

**As a** developer of any service
**I want** one identity shape regardless of provider
**So that** no service ever branches on Cognito versus Entra versus a password row.

### Acceptance criteria

1. **Given** any provider, **when** authentication succeeds, **then** the emitted principal carries `sub`,
   `email`, `realm` (`staff` or `customer`), `roles`, `branch_scope`, `iss` and `exp` — and nothing
   provider-specific.
2. **Given** a staff principal, **when** inspected, **then** `roles` is drawn from `hq`, `regional`,
   `branch_manager`, `staff`; **given** a customer principal, from `customer`, `guest`.
3. **Given** a guest, **when** a principal is issued, **then** `sub` is absent, `realm` is `customer` and
   `roles` is `["guest"]` — a guest is a real principal, not a null.
4. **Given** the schema, **when** it changes, **then** the token version changes and old tokens are rejected
   with a distinguishable error rather than silently misread.

### Out of scope

Provider adapters; those are FEAT-0006-02 and 04.

**OpenSpec change id:** `add-principal-model`

---

## STORY-0006-01-02 — Verification middleware

**As a** domain service
**I want** to trust the principal without calling anything
**So that** identity adds no latency or coupling to a request.

### Acceptance criteria

1. **Given** a request carrying a signed internal context header, **when** the middleware runs, **then** it
   verifies the signature and exposes a typed principal to the handler.
2. **Given** an unsigned or badly signed header, **when** received, **then** the request is rejected with 401
   and a stable code — never treated as anonymous.
3. **Given** an expired principal, **when** received, **then** the request is rejected as expired,
   distinguishable from unauthenticated.
4. **Given** a service reached directly rather than through the gateway, **when** it receives a forged header,
   **then** signature verification rejects it.
5. **Given** any rejection, **when** logged, **then** the trace id is recorded and the token is not.

### Out of scope

Authorization decisions; the middleware authenticates, scope enforcement is FEAT-0007-04.

**OpenSpec change id:** `add-verification-middleware`

---

## STORY-0006-01-03 — Seeded users for local development

**As a** developer
**I want** one real user per role seeded locally
**So that** scoping is exercised on a laptop rather than discovered in a cloud environment.

### Acceptance criteria

1. **Given** the seed, **when** applied, **then** it creates a staff user for each of `hq`, `regional` and
   `branch_manager` with differing `branch_scope`, plus a customer user.
2. **Given** the seeded users, **when** they authenticate, **then** they do so through the **real** password
   flow with argon2id hashing — no bypass, no header injection.
3. **Given** the seed, **when** run against an existing database, **then** it is idempotent.
4. **Given** seeded credentials, **when** documented, **then** they are clearly marked development-only and
   the seed refuses to run when the environment is not local.

### Notes

Criterion 4 is the guard that stops a convenience becoming a production account.

### Out of scope

Password reset and account recovery.

**OpenSpec change id:** `add-seeded-dev-users`
