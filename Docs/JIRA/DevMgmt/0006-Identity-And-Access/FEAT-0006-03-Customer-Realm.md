# FEAT-0006-03 — Customer Realm & Guest Migration

**Epic:** EPIC-0006 · **Depends on:** FEAT-0006-01 · **Blocks:** EPIC-0009, 0011, and the customer portal

## Description

`/v1/customer/*`: customer accounts, guest principals, and the migration path between them. Separate user
table, separate token audience, same principal shape.

---

## STORY-0006-03-01 — Customer accounts

**As a** customer
**I want** an account
**So that** my favourites, order history and gift cards follow me across devices.

### Acceptance criteria

1. **Given** `/v1/customer/register`, **when** called with an email and password, **then** an account is
   created, the password is hashed with argon2id, and a customer principal is issued.
2. **Given** an existing email, **when** registration is attempted, **then** the response does not reveal
   whether the account exists.
3. **Given** a customer principal, **when** presented to any service, **then** it carries `realm: customer`
   and can never satisfy a staff role check.
4. **Given** a customer token, **when** presented to a staff endpoint, **then** it is rejected on audience,
   not merely on role.

### Notes

Criterion 4 matters: audience separation means a customer token is not a staff token with fewer roles — it is
not a staff token at all.

### Out of scope

Email verification, social sign-in, password reset — each is its own story when needed.

**OpenSpec change id:** `add-customer-accounts`

---

## STORY-0006-03-02 — Guest principals

**As a** customer who just wants a coffee
**I want** to order without creating an account
**So that** nothing stands between me and a latte.

### Acceptance criteria

1. **Given** no credentials, **when** the portal starts a session, **then** a guest principal is issued with a
   durable anonymous identifier and no `sub`.
2. **Given** a guest principal, **when** used, **then** it can browse, build a cart, hold chat sessions and
   place an order.
3. **Given** a guest principal, **when** used against anything requiring an account — order history, wallet —
   **then** it is refused with a code the portal renders as an invitation to register, not an error.
4. **Given** a guest identifier, **when** it persists, **then** it survives a reload and is scoped to the
   device.

### Out of scope

Anonymous-to-anonymous transfer across devices.

**OpenSpec change id:** `add-guest-principals`

---

## STORY-0006-03-03 — Guest-to-account migration

**As a** guest who decides to register mid-order
**I want** everything I have done to come with me
**So that** signing up does not cost me my cart.

### Acceptance criteria

1. **Given** a guest with a cart, three chat sessions and possibly an in-flight order, **when** they register
   or sign in, **then** all of it is reassigned to the account atomically.
2. **Given** a guest who signs in to an **existing** account that already has a cart, **when** migration runs,
   **then** the conflict is resolved by a documented rule and the customer is told what happened — never a
   silent overwrite.
3. **Given** a gift card purchased as a guest, **when** the guest later registers with the same email, **then**
   that card appears in their wallet.
4. **Given** a migration failure, **when** it occurs, **then** nothing is partially moved and the guest session
   remains usable.

### Notes

This is the story most likely to be deferred and most painful to retrofit. Every later feature that binds
something to a principal — favourites, history, wallet — inherits this path, so it is cheaper to specify
before those exist than after.

### Out of scope

Merging two registered accounts.

**OpenSpec change id:** `add-guest-account-migration`
