# FEAT-0007-03 — Price Policy & Override Workflow

**Epic:** EPIC-0007 · **Depends on:** FEAT-0007-01, EPIC-0006 · **Blocks:** EPIC-0008

## Description

Who may change a price, by how much, and with what trail. The menu is brand-wide; overrides are the sanctioned
exception, and they are a workflow rather than an edit.

---

## STORY-0007-03-01 — Policy bands

**As** head office
**I want** price authority expressed as data
**So that** the limits are visible and adjustable rather than encoded in a service.

### Acceptance criteria

1. **Given** the model, **when** inspected, **then** a policy carries scope (brand, region or branch),
   item category, required role, maximum percentage delta, maximum absolute delta, and the threshold above
   which approval is required.
2. **Given** a proposed override, **when** evaluated, **then** the applicable policy is resolved
   most-specific-first and the resolution is explainable.
3. **Given** no applicable policy, **when** an override is proposed, **then** it is refused — the default is
   no authority, not unlimited authority.

### Out of scope

Approval chains longer than one step.

**OpenSpec change id:** `add-price-policy-bands`

---

## STORY-0007-03-02 — Override request and approval

**As a** regional manager
**I want** to propose an override and see it applied or escalated
**So that** regional pricing is a recorded decision.

### Acceptance criteria

1. **Given** a proposal inside the requester's band, **when** submitted with a reason, **then** it is applied
   immediately, effective-dated, and recorded with actor and timestamp.
2. **Given** a proposal above the threshold, **when** submitted, **then** it is stored as `pending`, is not
   applied, and is visible to HQ for approval or rejection.
3. **Given** a pending request, **when** approved, **then** the override becomes effective from its stated date
   and the approver is recorded.
4. **Given** any override, **when** queried, **then** its full history — proposer, reason, approver, effective
   window — is returned.
5. **Given** a request for a branch outside the requester's scope, **when** submitted, **then** it is refused
   by the API regardless of the policy band.

### Notes

Criteria 5 and the policy check are independent: scope says *which branches*, policy says *how much*. Both
must pass.

### Out of scope

Bulk overrides across a region in one request.

**OpenSpec change id:** `add-price-override-workflow`

---

## STORY-0007-03-03 — Effective price resolution

**As** `catalog-api`
**I want** one function that resolves the price of an item at a branch on a date
**So that** the customer portal, the agent and the order service never disagree.

### Acceptance criteria

1. **Given** an item, branch and date, **when** the price is resolved, **then** it returns the active override
   if one is effective, otherwise the brand base price.
2. **Given** overlapping overrides for the same item, branch and date, **when** they exist, **then** the save
   that created the overlap is refused rather than the resolution guessing.
3. **Given** a resolved price, **when** returned, **then** it states its source — base or override, and which
   override — so a surprising price is explainable.
4. **Given** an order line, **when** created, **then** it snapshots the resolved price, so a later override
   does not alter it.

### Out of scope

Promotional discounts; those are order-level in EPIC-0009.

**OpenSpec change id:** `add-effective-price-resolution`
