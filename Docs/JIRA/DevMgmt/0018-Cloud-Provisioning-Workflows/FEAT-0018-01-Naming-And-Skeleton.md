# FEAT-0018-01 — Naming, Numbering & Reusable Skeleton

**Epic:** EPIC-0018 · **Depends on:** container images from EPIC-0005 onward · **Blocks:** the rest of 0018

## Description

The convention and the shared machinery, so sixty workflow files are thin wrappers rather than sixty copies.

---

## STORY-0018-01-01 — Naming and numbering convention

**As a** team
**I want** workflow names to encode cloud, order and intent
**So that** the dependency order is visible in the file list.

### Acceptance criteria

1. **Given** the convention, **when** documented, **then** every workflow is named
   `<CLOUD>-<NNNN>-<Setup|Destroy>-<Target>.yml` with `CLOUD` in `AWS`, `AZURE`, `GCP`.
2. **Given** the number blocks, **when** documented, **then** they are: 0001–0099 network and identity,
   0100–0199 data plane, 0200–0299 images, 0300–0399 middleware, 0400–0499 portals, 0500–0599 observability,
   0900–0999 composites.
3. **Given** the rule, **when** stated, **then** **Setup runs ascending and Destroy runs descending**, and
   every workflow documents which block it belongs to.
4. **Given** a lint check, **when** run in CI, **then** it fails on any workflow whose filename does not match
   the convention or whose block is undeclared.

### Notes

The number is not decoration — it is the dependency order, and the lint is what keeps that true once there
are sixty files.

### Out of scope

Renaming existing CI workflows unrelated to provisioning.

**OpenSpec change id:** `add-workflow-naming-convention`

---

## STORY-0018-01-02 — Reusable workflow skeleton

**As a** developer
**I want** the deploy steps written once
**So that** sixty files do not each carry a copy.

### Acceptance criteria

1. **Given** `.github/workflows/_reusable/`, **when** inspected, **then** it holds `workflow_call` workflows
   for CloudFormation deploy, CloudFormation delete, Terraform apply and Terraform destroy.
2. **Given** any numbered workflow, **when** inspected, **then** it is a thin `workflow_dispatch` wrapper that
   passes inputs to a reusable workflow and contains no provisioning logic.
3. **Given** the reusable workflows, **when** they change, **then** the change applies to every block without
   editing the wrappers.
4. **Given** a wrapper, **when** it exceeds a documented line count, **then** the lint flags it as containing
   logic that belongs in the reusable workflow.

### Out of scope

A custom GitHub Action published to the marketplace.

**OpenSpec change id:** `add-reusable-workflow-skeleton`

---

## STORY-0018-01-03 — Shared stack contract

**As a** developer maintaining two IaC dialects
**I want** the interface between blocks written down once
**So that** CloudFormation and Terraform implement the same thing.

### Acceptance criteria

1. **Given** `DevOps/Cloud/contracts/`, **when** inspected, **then** each numbered block has a documented set
   of inputs and outputs, named identically across all three clouds.
2. **Given** a block's outputs, **when** consumed by a higher-numbered block, **then** the names match the
   contract, whether they arrive as CloudFormation exports or Terraform remote state outputs.
3. **Given** a contract change, **when** proposed, **then** it is reviewed against all three clouds together.
4. **Given** an implementation missing a contracted output, **when** validated, **then** CI fails.

### Notes

This contract is the mitigation for the deliberate cost of two dialects. Without it, AWS races ahead and the
other two quietly become stale.

### Out of scope

Generating one dialect from the other.

**OpenSpec change id:** `add-shared-stack-contract`
