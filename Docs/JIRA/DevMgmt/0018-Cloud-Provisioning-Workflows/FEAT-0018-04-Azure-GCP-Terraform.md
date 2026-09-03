# FEAT-0018-04 — Azure & GCP Terraform Blocks

**Epic:** EPIC-0018 · **Depends on:** FEAT-0018-02 · **Blocks:** FEAT-0018-05

## Description

The same numbered blocks in Terraform, one state per block. GCP Cloud Run is the primary target; Azure
Container Apps is the equivalent second.

---

## STORY-0018-04-01 — State layout and backends

**As an** operator
**I want** one Terraform state per numbered block per environment
**So that** a block can be destroyed independently.

### Acceptance criteria

1. **Given** the layout, **when** inspected, **then** each block has its own state, stored in GCS for GCP and
   Azure Storage for Azure, keyed by cloud, environment and block.
2. **Given** a block consuming another's outputs, **when** it runs, **then** it reads them via remote state
   using the contracted output names.
3. **Given** concurrent runs on one state, **when** they occur, **then** state locking prevents corruption.
4. **Given** the backend bootstrap, **when** documented, **then** the one-time manual creation of the state
   bucket or container is written down.

### Out of scope

Terraform Cloud or a hosted state service.

**OpenSpec change id:** `add-terraform-state-layout`

---

## STORY-0018-04-02 — GCP blocks

**As an** operator
**I want** the primary cloud target working end to end
**So that** there is a near-zero-cost environment that stays up.

### Acceptance criteria

1. **Given** the GCP blocks, **when** run in order, **then** they provision networking, identity via workload
   identity federation, service connectivity, Cloud Run services and portal hosting.
2. **Given** Cloud Run services, **when** configured, **then** they scale to zero with minimum instances at
   zero, so an idle environment costs effectively nothing.
3. **Given** the services, **when** deployed, **then** they pull from `ghcr.io` and receive configuration as
   environment variables — the same images as everywhere else.
4. **Given** the tax rate cache, **when** running on Cloud Run, **then** the readiness signal is honoured so an
   instance that cannot load rates does not take traffic.

### Notes

Criterion 4 is the Kubernetes note made concrete: dropping k8s did not change the cache design, and this is
where that is verified.

### Out of scope

Custom domains and managed certificates.

**OpenSpec change id:** `add-gcp-terraform-blocks`

---

## STORY-0018-04-03 — Azure blocks

**As an** operator
**I want** the same environment reproducible on Azure
**So that** the multi-cloud claim is demonstrated, not asserted.

### Acceptance criteria

1. **Given** the Azure blocks, **when** run in order, **then** they provision the resource group, VNet, managed
   identity, Container Apps environment, the services and portal hosting.
2. **Given** Container Apps, **when** configured, **then** they scale to zero.
3. **Given** the deployment, **when** compared with GCP, **then** the same images run and only environment
   variables differ — including `AUTH_STAFF_PROVIDER=entra_id` and the chat provider.
4. **Given** every Azure and GCP Destroy workflow, **when** run out of order, **then** the ordering guard
   refuses, matching CloudFormation's behaviour.

### Out of scope

Azure Front Door and CDN configuration.

**OpenSpec change id:** `add-azure-terraform-blocks`
