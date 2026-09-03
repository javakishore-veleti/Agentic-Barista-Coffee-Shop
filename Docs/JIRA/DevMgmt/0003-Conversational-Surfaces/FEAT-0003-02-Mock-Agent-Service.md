# FEAT-0003-02 — Mock Agent Service

**Epic:** EPIC-0003 · **Depends on:** FEAT-0003-01 · **Blocks:** FEAT-0003-03, FEAT-0003-04

## Description

A small FastAPI service that implements the chat envelope exactly and returns scripted responses. It exists so
the UI is complete and demonstrable before any agent framework is introduced, and it survives afterwards as
the fastest way to reproduce a front-end bug without a model in the loop.

---

## STORY-0003-02-01 — Envelope-conformant mock

**As a** front-end developer
**I want** a service that streams the real envelope
**So that** I build against the contract rather than a fixture.

### Acceptance criteria

1. **Given** the mock, **when** it receives a chat request, **then** it streams `token` events with realistic
   inter-token delays, then `done` with plausible token counts and cost.
2. **Given** a scripted scenario, **when** triggered, **then** the mock emits `tool_call` and `tool_result`
   events with realistic latency before continuing to stream tokens.
3. **Given** the mock, **when** validated, **then** every event it emits passes the schema from
   `STORY-0003-01-01`, enforced by a test that fails the build.
4. **Given** a request with no `branch_id`, **when** received, **then** the mock returns a 400 with the same
   error code the real agents will use.

### Out of scope

Any model call. The mock is deterministic and offline.

**OpenSpec change id:** `add-mock-agent-service`

---

## STORY-0003-02-02 — Scripted scenarios per surface

**As a** developer or reviewer
**I want** named scenarios covering the paths that matter
**So that** the UI is exercised against the behaviour the real agents must eventually produce.

### Acceptance criteria

1. **Given** the barista surface, **when** scenarios are listed, **then** they include: an in-menu
   recommendation, an out-of-menu refusal, a dairy-free request satisfied by an oat milk substitution, a
   clarifying question for a vague request, and an item unavailable at the selected branch.
2. **Given** the events surface, **when** scenarios are listed, **then** they include: a quote for a 2-hour
   weeknight booking, a Sunday 3-hour booking, a request for a day the branch cannot host, and a booking that
   pauses awaiting staff approval.
3. **Given** the gift cards surface, **when** scenarios are listed, **then** they include: a $25 custom-amount
   purchase, a group card, a scheduled delivery, and a balance check.
4. **Given** any scenario, **when** selected by header or query parameter, **then** it replays identically
   every time.

### Notes

These scenarios become the behavioural eval suite in epic 0017. Writing them here, as a product statement of
what good looks like, is cheaper than reverse-engineering them from three finished agents.

### Out of scope

Scenario authoring UI.

**OpenSpec change id:** `add-mock-scenarios`

---

## STORY-0003-02-03 — Failure injection

**As a** developer
**I want** the mock to fail on demand
**So that** error and retry paths are built and seen, not assumed.

### Acceptance criteria

1. **Given** a failure mode parameter, **when** set, **then** the mock can produce: a mid-stream disconnect, a
   provider timeout error event, a slow first token, a malformed `ui_action`, and a 500 before any event.
2. **Given** each failure mode, **when** triggered, **then** the chat panel renders a distinct, correct state
   and the user can recover without reloading the page.

### Out of scope

Chaos testing infrastructure.

**OpenSpec change id:** `add-mock-failure-injection`
