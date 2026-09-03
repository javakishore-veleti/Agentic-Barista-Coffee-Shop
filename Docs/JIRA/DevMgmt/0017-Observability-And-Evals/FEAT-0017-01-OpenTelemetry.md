# FEAT-0017-01 — OpenTelemetry End to End

**Epic:** EPIC-0017 · **Depends on:** EPIC-0005 (partially deliverable from then) · **Blocks:** FEAT-0017-04

## Description

One trace id from the browser through gateway, agent, MCP server and domain API.

---

## STORY-0017-01-01 — Trace propagation

**As a** developer debugging a slow answer
**I want** one trace covering the whole request
**So that** I can see where the time went.

### Acceptance criteria

1. **Given** a request from either portal, **when** it enters the gateway, **then** a trace is started or
   continued and propagated through every downstream hop.
2. **Given** a trace, **when** viewed, **then** it contains spans for gateway, agent turn, each model call,
   each MCP tool call, each domain API call and each database query.
3. **Given** an SSE stream, **when** traced, **then** the turn is one trace with the time to first token
   visible as its own span.
4. **Given** an error anywhere, **when** it occurs, **then** the span is marked failed with the stable error
   code, and the trace id appears in the client-visible error.

### Notes

Criterion 4 is what makes a customer-reported problem findable: they quote a code and a trace id, and the
whole path is retrievable.

### Out of scope

Sampling strategy for high volume.

**OpenSpec change id:** `add-otel-trace-propagation`

---

## STORY-0017-01-02 — Local observability stack

**As a** developer
**I want** traces visible locally
**So that** observability is used during development, not added before launch.

### Acceptance criteria

1. **Given** `docker-all-up.sh`, **when** it runs, **then** an OTel collector and a trace UI start as part of
   the observability component.
2. **Given** a request made against the local stack, **when** it completes, **then** its trace is viewable
   within seconds.
3. **Given** the observability component, **when** brought down, **then** the rest of the stack continues to
   work with tracing disabled.

### Out of scope

Metrics dashboards and log aggregation.

**OpenSpec change id:** `add-local-observability-stack`

---

## STORY-0017-01-03 — Agent-specific spans

**As a** developer
**I want** agent internals visible as spans
**So that** "why did it do that" is answerable.

### Acceptance criteria

1. **Given** an agent turn, **when** traced, **then** each tool call span records the tool name, argument keys,
   duration and result size — but not full argument values by default.
2. **Given** a LangGraph turn, **when** traced, **then** each node entry and exit is a span, so the path
   through the graph is visible.
3. **Given** a guardrail block, **when** it occurs, **then** it appears as a span with the reason.
4. **Given** sensitive data, **when** spans are recorded, **then** message content is excluded unless a debug
   flag is set, and that flag is refused outside local environments.

### Out of scope

Full prompt and completion capture in shared environments.

**OpenSpec change id:** `add-agent-spans`
