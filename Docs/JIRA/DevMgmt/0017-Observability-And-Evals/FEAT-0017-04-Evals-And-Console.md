# FEAT-0017-04 — Behavioural Evals & Agent Console

**Epic:** EPIC-0017 · **Depends on:** FEAT-0017-01, FEAT-0003-02 · **Blocks:** nothing

## Description

The mock scenarios from `FEAT-0003-02`, promoted into an automated eval suite — plus the console where staff
see what agents are doing.

---

## STORY-0017-04-01 — Behavioural eval suite

**As a** team
**I want** grounding regressions to fail a build
**So that** they are not discovered in a demo.

### Acceptance criteria

1. **Given** the suite, **when** run, **then** it covers, at minimum: an in-menu recommendation; an out-of-menu
   refusal; a dairy-free request satisfied by substitution; an item on the brand menu but unavailable at the
   branch; a vague request drawing exactly one clarifying question; a Sunday quote stating the correct room
   window; a booking that reaches the approval interrupt; a gift card balance check; and a scheduled delivery
   stating its timezone.
2. **Given** each case, **when** evaluated, **then** it asserts checkable properties — which items were named,
   which tools were called, whether a figure traces to a tool result — rather than comparing prose.
3. **Given** the suite, **when** it fails, **then** the failure names the case and the property.
4. **Given** the suite, **when** run in CI, **then** it runs against the self-hosted default so it costs
   nothing.

### Notes

These are the same scenarios written as a product statement in `FEAT-0003-02` before any agent existed.
Promoting them here is cheaper and more honest than reverse-engineering evals from three finished agents.

### Out of scope

Human preference scoring and LLM-as-judge grading.

**OpenSpec change id:** `add-behavioural-eval-suite`

---

## STORY-0017-04-02 — Agent console

**As** head office
**I want** to see what the agents are doing
**So that** problems are visible before customers report them.

### Acceptance criteria

1. **Given** the console, **when** opened by an authorised principal, **then** it lists recent conversations by
   surface with duration, turn count, tool calls, cost and outcome.
2. **Given** a conversation, **when** opened, **then** its trace is shown — every tool call, every node
   transition, every guardrail block.
3. **Given** guardrail blocks, **when** they occur, **then** they are surfaced prominently, because a rising
   block rate is the earliest signal of a grounding regression.
4. **Given** message content, **when** displayed, **then** access is restricted by role and the access is
   audited.

### Out of scope

Live intervention in a running conversation.

**OpenSpec change id:** `add-agent-console`

---

## STORY-0017-04-03 — Prompt and configuration versioning

**As a** developer
**I want** to know which prompt and configuration produced a conversation
**So that** a behaviour change is attributable.

### Acceptance criteria

1. **Given** any turn, **when** recorded, **then** it carries the prompt version, model, provider and toolset
   hash.
2. **Given** a prompt change, **when** deployed, **then** the version increments and the change is visible in
   the console.
3. **Given** an eval result, **when** recorded, **then** it references the prompt version it was produced
   against.
4. **Given** two conversations with different outcomes, **when** compared, **then** whether they ran the same
   configuration is answerable without guessing.

### Out of scope

A prompt authoring UI.

**OpenSpec change id:** `add-prompt-versioning`
