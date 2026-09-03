# FEAT-0014-01 — Agent, Tools & Provider Binding

**Epic:** EPIC-0014 · **Depends on:** EPIC-0013 · **Blocks:** the rest of EPIC-0014

## Description

The ADK agent itself, attached to `catalog-mcp` and the orders write server, bound to a provider through the
seam.

---

## STORY-0014-01-00 — Tool-calling conformance spike

**As a** team
**I want** to know whether the configured model can call our tools reliably
**So that** we do not build an agent on a foundation that cannot hold it.

### Acceptance criteria

1. **Given** the configured default model, **when** the spike runs, **then** it measures: correct tool
   selection, correct argument types, correct handling of a **required** `branch_id`, behaviour on a
   multi-step task, and refusal behaviour when no tool fits.
2. **Given** the spike, **when** run, **then** it exercises ADK's native Gemini path **and** the LiteLLM path
   to a self-hosted model, and reports both.
3. **Given** results below a documented threshold, **when** recorded, **then** the model is marked unsupported
   for this surface and the fallback is exercised before agent work continues.
4. **Given** the spike, **when** complete, **then** its results are committed, not just discussed.

### Notes

**This story runs before any other work in this epic.** ADK reaching a small self-hosted model through
LiteLLM is the single likeliest thing to force a redesign, and the $0 default puts it on the critical path.
If it fails, the fallback is a free-tier hosted model for this surface only — the seam and the other two
agents are unaffected.

### Out of scope

Fixing the model. The outcome is a supported/unsupported decision, not a tuning project.

**OpenSpec change id:** `add-tool-calling-conformance-spike`

---

## STORY-0014-01-01 — Agent and MCP attachment

**As a** developer
**I want** the barista agent attached to MCP tools
**So that** it uses the shared definitions rather than its own.

### Acceptance criteria

1. **Given** the agent, **when** assembled, **then** it attaches `catalog-mcp` and the orders write server
   through ADK's MCP toolset — it defines no tools of its own.
2. **Given** the toolset, **when** inspected at runtime, **then** it contains exactly the tools those servers
   publish, and the set is logged at startup.
3. **Given** a tool error, **when** returned, **then** the agent surfaces the structured reason to the user
   rather than retrying blindly.
4. **Given** a required parameter the agent cannot supply, **when** it occurs, **then** the agent asks the user
   rather than guessing a value.

### Out of scope

Any locally-defined tool. If one seems necessary, it belongs on an MCP server.

**OpenSpec change id:** `add-barista-agent-mcp-attachment`

---

## STORY-0014-01-02 — Provider binding

**As an** operator
**I want** the barista's model set by configuration
**So that** the fallback is a variable, not a rewrite.

### Acceptance criteria

1. **Given** `BARISTA_LLM_PROVIDER=gemini`, **when** the agent starts, **then** it uses ADK's native model;
   **given** any other provider, **then** it uses the LiteLLM path.
2. **Given** an unsupported provider per the conformance results, **when** configured, **then** the service
   refuses to start with a message naming the conformance finding.
3. **Given** the resolved model, **when** the service starts, **then** it is logged and reported on `/readyz`.

### Notes

Criterion 2 makes the conformance suite load-bearing rather than advisory.

### Out of scope

Runtime model switching per conversation.

**OpenSpec change id:** `add-barista-provider-binding`
