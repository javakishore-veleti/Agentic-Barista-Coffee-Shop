# FEAT-0006-04 — Cloud Provider Adapters

**Epic:** EPIC-0006 · **Depends on:** FEAT-0006-01 · **Blocks:** EPIC-0018

## Description

Cognito, Entra ID and GCP Identity Platform as verify-and-map adapters. In these modes `authz-api` stops
issuing tokens and starts translating someone else's.

---

## STORY-0006-04-01 — Verifier adapters

**As an** operator deploying to a cloud
**I want** the platform to accept that cloud's identity provider
**So that** staff use corporate credentials without the application changing.

### Acceptance criteria

1. **Given** `AUTH_STAFF_PROVIDER=cognito`, **when** a pool JWT is presented, **then** its signature, issuer,
   audience and expiry are verified against the pool's published keys.
2. **Given** `entra_id` or `gcp_identity`, **when** configured, **then** the equivalent verification applies.
3. **Given** any verifier, **when** signing keys rotate, **then** they are refetched and cached without a
   restart.
4. **Given** a verified external token, **when** mapped, **then** the resulting principal is identical in
   shape to one issued by `basic`.
5. **Given** a verifier configured with an unreachable provider, **when** the service starts, **then** it
   fails readiness rather than accepting every token or rejecting every token silently.

### Out of scope

Provisioning the pools and directories themselves; that is EPIC-0018.

**OpenSpec change id:** `add-idp-verifier-adapters`

---

## STORY-0006-04-02 — Claim mapping

**As** head office
**I want** provider groups mapped to our roles and branch scope
**So that** directory membership drives access.

### Acceptance criteria

1. **Given** a configurable mapping, **when** an external token is verified, **then** provider groups or claims
   are translated to `roles` and `branch_scope`.
2. **Given** a claim with no mapping, **when** encountered, **then** it is ignored and recorded, never guessed
   into a role.
3. **Given** a principal that maps to no role at all, **when** produced, **then** access is denied with a
   message distinguishing "unknown user" from "no access configured".
4. **Given** the mapping, **when** changed, **then** it takes effect without a code change.

### Out of scope

Automatic directory synchronisation and group provisioning.

**OpenSpec change id:** `add-claim-mapping`

---

## STORY-0006-04-03 — Provider parity tests

**As a** developer
**I want** one test suite proving all four providers behave identically
**So that** "configurable" is demonstrated rather than asserted.

### Acceptance criteria

1. **Given** the parity suite, **when** run, **then** it exercises the same authentication and authorization
   scenarios against every configured provider.
2. **Given** any provider producing a differently shaped principal, **when** the suite runs, **then** it fails
   naming the field and the provider.
3. **Given** cloud providers unavailable in local development, **when** the suite runs locally, **then** those
   cases are exercised against recorded fixtures and clearly reported as fixture-backed.

### Notes

Same principle as the repository contract suite: a seam that is not tested across its implementations quietly
stops being a seam.

### Out of scope

Live cloud integration tests in CI; those run in the environment epic 0018 provisions.

**OpenSpec change id:** `add-provider-parity-tests`
