# FEAT-0006-02 — Staff Realm

**Epic:** EPIC-0006 · **Depends on:** FEAT-0006-01 · **Blocks:** EPIC-0007, admin portal wiring

## Description

`/v1/staff/*`: staff authentication, role assignment and branch scope management, backed by `basic` locally
and by a corporate provider in cloud.

---

## STORY-0006-02-01 — Staff authentication

**As a** staff member
**I want** to sign in and receive a token carrying my role and scope
**So that** every service knows what I may do.

### Acceptance criteria

1. **Given** `AUTH_STAFF_PROVIDER=basic`, **when** valid credentials are posted to `/v1/staff/login`, **then**
   a token is issued carrying the principal, and passwords are verified with argon2id.
2. **Given** invalid credentials, **when** posted, **then** the response is indistinguishable between unknown
   user and wrong password, and is rate limited.
3. **Given** a valid token, **when** presented to `/v1/staff/refresh` before expiry, **then** a new token is
   issued; after expiry, refresh fails and re-authentication is required.
4. **Given** a token, **when** posted to `/v1/staff/logout`, **then** it is revoked and subsequent use fails.

### Out of scope

MFA, SSO enrolment.

**OpenSpec change id:** `add-staff-authentication`

---

## STORY-0006-02-02 — Role and branch scope administration

**As** head office
**I want** to assign roles and branch scope to staff
**So that** the permission matrix is administered rather than seeded.

### Acceptance criteria

1. **Given** an HQ principal, **when** they assign a role and branch scope to a staff user, **then** the change
   takes effect on that user's next token and is recorded with actor and timestamp.
2. **Given** a non-HQ principal, **when** they attempt to grant a role at or above their own, **then** the
   request is refused.
3. **Given** a `branch_scope`, **when** set, **then** it is either `["*"]` or an explicit list of branch ids;
   an empty scope is rejected as a likely mistake.
4. **Given** a scope change, **when** applied, **then** already-issued tokens retain the old scope until they
   expire, and the expiry window is documented.

### Notes

Criterion 4 is an honest statement of a real trade-off: short-lived tokens mean scope changes take effect
within the TTL. If immediate revocation is ever required, that is a separate story with a revocation list,
not a silent assumption here.

### Out of scope

Delegated administration and approval chains for role grants.

**OpenSpec change id:** `add-staff-role-administration`

---

## STORY-0006-02-03 — Staff audit trail

**As** head office
**I want** authentication and authorization events recorded
**So that** "who changed that price" is answerable.

### Acceptance criteria

1. **Given** any sign-in, sign-out, role change or scope change, **when** it occurs, **then** an audit row is
   written with actor, subject, action, timestamp and trace id.
2. **Given** a scope violation refused by any domain API, **when** it occurs, **then** it is recorded as an
   audit event, not only a log line.
3. **Given** the audit table, **when** queried, **then** it is append-only and has no update or delete path
   through the API.

### Out of scope

Log shipping, retention policy and SIEM integration.

**OpenSpec change id:** `add-staff-audit-trail`
