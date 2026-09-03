# FEAT-0017-02 — Token & Cost Accounting

**Epic:** EPIC-0017 · **Depends on:** FEAT-0017-01 · **Blocks:** FEAT-0017-04

## Description

Knowing what a conversation costs — which matters more, not less, under a $1 budget.

---

## STORY-0017-02-01 — Per-turn accounting

**As an** operator
**I want** every turn's token usage and cost recorded
**So that** spend is attributable.

### Acceptance criteria

1. **Given** any agent turn, **when** it completes, **then** input tokens, output tokens, model, provider and
   an estimated cost are recorded as span attributes and returned in the `done` event.
2. **Given** a self-hosted model, **when** accounted, **then** cost is recorded as zero and the duration and
   token counts are still captured, because compute is the real cost there.
3. **Given** a turn with several model calls, **when** accounted, **then** the total covers all of them.
4. **Given** a cost estimate, **when** produced, **then** the rate table used is versioned so historical
   figures stay explainable.

### Out of scope

Billing integration and chargeback.

**OpenSpec change id:** `add-per-turn-cost-accounting`

---

## STORY-0017-02-02 — Aggregation by surface and provider

**As** a product owner
**I want** cost broken down
**So that** I can see which surface is expensive.

### Acceptance criteria

1. **Given** recorded turns, **when** aggregated, **then** cost and token counts are available by surface, by
   provider, by day and by branch.
2. **Given** the aggregation, **when** queried, **then** it answers "what did the barista cost yesterday"
   without a bespoke query.
3. **Given** a provider change, **when** it happens, **then** before and after are comparable in the same view.

### Out of scope

Forecasting and budget alerts.

**OpenSpec change id:** `add-cost-aggregation`

---

## STORY-0017-02-03 — Budget guard

**As an** operator on a $1 budget
**I want** a hard stop
**So that** a misconfiguration cannot produce a surprising bill.

### Acceptance criteria

1. **Given** a configured daily cost ceiling per surface, **when** it is reached, **then** further paid-provider
   calls are refused with a clear error and the surface degrades to a stated message.
2. **Given** a self-hosted provider, **when** configured, **then** the ceiling does not apply.
3. **Given** the ceiling being hit, **when** it happens, **then** it is recorded prominently and is visible in
   the agent console.
4. **Given** a ceiling, **when** it is misconfigured as zero or absent, **then** the service refuses to start
   with a paid provider.

### Notes

Criterion 4 is deliberate: under this posture, running a paid provider with no ceiling should be a decision
someone has to make explicitly.

### Out of scope

Per-customer quotas.

**OpenSpec change id:** `add-budget-guard`
