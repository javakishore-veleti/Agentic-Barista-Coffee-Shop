# FEAT-0018-02 — OIDC Federation & Safety Rails

**Epic:** EPIC-0018 · **Depends on:** FEAT-0018-01 · **Blocks:** FEAT-0018-03, 04

## Description

No long-lived cloud keys, and no accidental teardowns.

---

## STORY-0018-02-01 — OIDC federation

**As an** operator
**I want** workflows to authenticate without stored keys
**So that** a leaked secret cannot become cloud access.

### Acceptance criteria

1. **Given** any provisioning workflow, **when** it authenticates, **then** it uses GitHub OIDC federation to
   assume a role or identity — no long-lived access keys exist in repository secrets.
2. **Given** the trust policy, **when** configured, **then** it is scoped to this repository and, where
   supported, to the specific environment.
3. **Given** a workflow for a cloud whose federation is not configured, **when** run, **then** it fails with a
   message naming the missing trust configuration.
4. **Given** the bootstrap for federation itself, **when** documented, **then** the one-time manual steps are
   written down, because that step cannot bootstrap itself.

### Out of scope

Managing cloud organisation structure and account creation.

**OpenSpec change id:** `add-oidc-federation`

---

## STORY-0018-02-02 — Destroy safety rails

**As an** operator
**I want** teardown to be deliberate
**So that** a mis-click does not delete an environment.

### Acceptance criteria

1. **Given** any `Destroy` workflow, **when** dispatched, **then** it requires an `environment` input and a
   typed `confirm` input that must exactly match the environment name.
2. **Given** a mismatch, **when** submitted, **then** the job fails immediately before any provider call.
3. **Given** a `concurrency` group keyed on cloud, environment and block, **when** a Setup and a Destroy are
   dispatched together, **then** they cannot run simultaneously on the same stack.
4. **Given** no workflow, **when** the repository receives a push, **then** nothing provisions or destroys —
   every provisioning workflow is `workflow_dispatch` only, enforced by lint.

### Out of scope

Approval environments requiring a second reviewer; the typed confirmation is the control here.

**OpenSpec change id:** `add-destroy-safety-rails`

---

## STORY-0018-02-03 — Ordering guard

**As an** operator
**I want** out-of-order teardown refused
**So that** I cannot orphan resources.

### Acceptance criteria

1. **Given** AWS, **when** a Destroy is attempted on a stack whose exports are imported by a higher block,
   **then** CloudFormation refuses and the workflow surfaces that refusal clearly.
2. **Given** Azure or GCP, **when** a Destroy is attempted, **then** the reusable workflow **checks for
   higher-numbered stacks** in that environment and refuses if any exist.
3. **Given** that check, **when** implemented, **then** it lives in the reusable workflow, written once — not
   in each block.
4. **Given** a refusal, **when** it occurs, **then** the message names which higher block is still present.

### Notes

AWS gets this guarantee from the platform; Terraform gives no equivalent across separate states. Writing the
check once means Azure and GCP behave the same way without sixty copies of the same guard.

### Out of scope

Automatic cascading teardown.

**OpenSpec change id:** `add-teardown-ordering-guard`
