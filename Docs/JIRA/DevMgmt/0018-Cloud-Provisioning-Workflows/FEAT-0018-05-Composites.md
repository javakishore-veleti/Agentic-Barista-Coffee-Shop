# FEAT-0018-05 — Composite Setup-All / Destroy-All

**Epic:** EPIC-0018 · **Depends on:** FEAT-0018-03, FEAT-0018-04 · **Blocks:** nothing

## Description

The 0900-block workflows that chain the numbered blocks in order — ascending to build, descending to tear down.

---

## STORY-0018-05-01 — Composite setup

**As an** operator
**I want** one dispatch that builds a whole environment
**So that** a demo does not need twelve manual runs.

### Acceptance criteria

1. **Given** `<CLOUD>-0900-Setup-All`, **when** dispatched, **then** it calls each block's reusable workflow in
   **ascending** numeric order, waiting for each to succeed.
2. **Given** a block failing, **when** it fails, **then** the composite stops, reports which block and why, and
   does not attempt later blocks.
3. **Given** a partially built environment, **when** the composite is re-run, **then** already-complete blocks
   are idempotent and it continues rather than failing on existing resources.
4. **Given** completion, **when** it succeeds, **then** it outputs the environment's entry URLs and runs a
   smoke check against them.

### Out of scope

Parallelising independent blocks; ordered and simple is the point.

**OpenSpec change id:** `add-composite-setup-all`

---

## STORY-0018-05-02 — Composite destroy

**As an** operator
**I want** one dispatch that removes everything
**So that** the demo-only posture is practical.

### Acceptance criteria

1. **Given** `<CLOUD>-0999-Destroy-All`, **when** dispatched with the typed confirmation, **then** it calls each
   block in **descending** numeric order.
2. **Given** a block failing to destroy, **when** it fails, **then** the composite stops and names the block
   and the resource holding it, rather than continuing and orphaning things.
3. **Given** completion, **when** it succeeds, **then** a verification step confirms no resources remain in
   that environment and fails the run if any do.
4. **Given** the composite, **when** documented, **then** its expected duration and cost are recorded so
   running it is a routine decision.

### Notes

Under the $1 posture this is not emergency equipment — it runs the same afternoon as every AWS demo, which is
exactly why it must be trustworthy enough to run without reading the console first.

### Out of scope

Scheduled automatic teardown; the decision stays manual.

**OpenSpec change id:** `add-composite-destroy-all`
