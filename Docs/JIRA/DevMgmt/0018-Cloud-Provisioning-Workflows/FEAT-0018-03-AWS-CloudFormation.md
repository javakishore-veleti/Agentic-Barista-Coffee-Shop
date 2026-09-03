# FEAT-0018-03 — AWS CloudFormation Blocks

**Epic:** EPIC-0018 · **Depends on:** FEAT-0018-02 · **Blocks:** FEAT-0018-05

## Description

One CloudFormation stack per numbered block, with cross-stack exports carrying the contract. AWS is the
demo-only target: provision, demonstrate, destroy the same day.

---

## STORY-0018-03-01 — Foundation and data blocks

**As an** operator
**I want** network, identity and data provisioned as ordered stacks
**So that** everything above them has something to attach to.

### Acceptance criteria

1. **Given** `AWS-0001-Setup-Network`, **when** run, **then** it creates the VPC and subnets and **exports**
   the contracted outputs.
2. **Given** `AWS-0010-Setup-Identity`, **when** run, **then** it creates the task roles the services need,
   scoped to least privilege.
3. **Given** `AWS-0100-Setup-Postgres`, **when** run, **then** it provisions the data-plane connectivity for
   the configured Postgres — which under the budget is an external free-tier instance, so the stack manages
   secrets and networking rather than a database instance.
4. **Given** each stack, **when** it completes, **then** its exports match the shared contract exactly.
5. **Given** each `Destroy` counterpart, **when** run out of order, **then** CloudFormation refuses because the
   exports are still imported.

### Notes

Criterion 3 is the budget showing up in the architecture: the cheapest correct answer is one external Postgres
reachable from every cloud, so the AWS data block manages access to it rather than duplicating it.

### Out of scope

Multi-AZ, backups and disaster recovery.

**OpenSpec change id:** `add-aws-foundation-stacks`

---

## STORY-0018-03-02 — Service and portal blocks

**As an** operator
**I want** the services and portals deployed as stacks
**So that** a demo environment is one dispatch away.

### Acceptance criteria

1. **Given** `AWS-0300-Setup-DomainApis` and `AWS-0310-Setup-AgentServices`, **when** run, **then** they deploy
   the services as containers pulling images from `ghcr.io`, importing network and identity exports.
2. **Given** the deployments, **when** configured, **then** every provider, store and auth setting comes from
   stack parameters — the images are identical to those run locally.
3. **Given** `AWS-0400-Setup-Portals`, **when** run, **then** the portals are served with the gateway URL
   injected at deploy time, not baked into the image.
4. **Given** any service stack, **when** it completes, **then** a smoke check confirms `/healthz` before the
   workflow reports success.

### Notes

There is no registry block for AWS. Images come from `ghcr.io`, which removes ECR from the numbering entirely
and is why the same images run on all three clouds.

### Out of scope

Autoscaling policy and load testing.

**OpenSpec change id:** `add-aws-service-stacks`

---

## STORY-0018-03-03 — Demo cycle validation

**As an** operator on a $1 budget
**I want** a full up-and-down cycle proven
**So that** AWS never accrues standing cost.

### Acceptance criteria

1. **Given** a scratch account, **when** `Setup-All` then `Destroy-All` is run, **then** no resources remain —
   verified by a post-teardown check, not by assumption.
2. **Given** the cycle, **when** measured, **then** its cost is recorded and is under a dollar.
3. **Given** the teardown check, **when** it finds an orphan, **then** the workflow fails and names it.
4. **Given** the cycle, **when** documented, **then** the expected duration of each block is recorded.

### Notes

Criterion 1 is the whole reason AWS got CloudFormation. A teardown that leaves an orphaned NAT gateway costs
more per month than this entire budget.

### Out of scope

Cost anomaly alerting.

**OpenSpec change id:** `add-aws-demo-cycle-validation`
