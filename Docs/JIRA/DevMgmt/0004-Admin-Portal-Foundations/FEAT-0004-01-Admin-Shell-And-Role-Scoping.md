# FEAT-0004-01 — Admin Shell & Role Scoping

**Epic:** EPIC-0004 · **Depends on:** FEAT-0001-02 · **Blocks:** FEAT-0004-02, 03, 04

## Description

The admin application shell, and the permission model every later admin screen is written against.

---

## STORY-0004-01-01 — Permission matrix

**As a** product owner
**I want** the role and branch-scope rules written down as one reviewable matrix
**So that** `authz-api` and every domain API implement the same thing.

### Acceptance criteria

1. **Given** the matrix, **when** reviewed, **then** for every admin capability it names the required role
   (`hq`, `regional`, `branch_manager`, `staff`) and whether branch scope applies.
2. **Given** the matrix, **when** reviewed, **then** it records that menu item definitions are **HQ-only**,
   branch availability and daily rotation are **branch-scoped**, event rate cards are **regional**, and
   price overrides follow the policy bands in the branch model.
3. **Given** the matrix, **when** it changes, **then** the change is visible in one file rather than spread
   across screens.

### Notes

This artefact is an input to EPIC-0006, not a summary of it. Writing it after the auth service exists means
the service's accidental behaviour becomes the specification.

### Out of scope

Implementation of enforcement.

**OpenSpec change id:** `add-admin-permission-matrix`

---

## STORY-0004-01-02 — Admin shell and scoped navigation

**As a** branch manager
**I want** to see only what I can act on
**So that** I am not navigating a console built for head office.

### Acceptance criteria

1. **Given** a signed-in principal, **when** the shell renders, **then** navigation shows only entries the
   principal's role permits, and the current role and branch scope are visible at all times.
2. **Given** a principal with scope over several branches, **when** the shell renders, **then** a branch
   selector limits every screen to one branch at a time, with an explicit "all my branches" option where a
   screen supports it.
3. **Given** a principal with `branch_scope: ["*"]`, **when** the shell renders, **then** the selector offers
   search across all 1,000 branches rather than an unusable flat list.
4. **Given** a route the principal may not use, **when** reached directly by URL, **then** a clear
   not-authorised state renders — not a blank screen, and not a redirect that hides the reason.

### Out of scope

Impersonation and support-access flows.

**OpenSpec change id:** `add-admin-shell-scoping`

---

## STORY-0004-01-03 — Sign-in against the two realms

**As a** staff member
**I want** to sign in to the admin portal with my staff credentials
**So that** my role and branch scope drive what I see.

### Acceptance criteria

1. **Given** the admin portal, **when** I sign in, **then** it authenticates against the **staff** realm
   (`/v1/staff/*`), never the customer realm.
2. **Given** a valid session, **when** the token nears expiry, **then** it refreshes silently and an
   unrecoverable failure returns me to sign-in with my place preserved where safe.
3. **Given** a fixture auth mode for local development, **when** enabled, **then** I can switch between one
   seeded user per role to exercise scoping without standing up an identity provider.
4. **Given** sign-out, **when** invoked, **then** tokens are cleared and cached scoped data is discarded.

### Notes

The seeded-user switcher is a development affordance over a **real** token flow, not a bypass of it. Local
auth is real auth; the switcher just picks which real user you are.

### Out of scope

MFA, SSO enrolment, password reset flows.

**OpenSpec change id:** `add-admin-signin`
