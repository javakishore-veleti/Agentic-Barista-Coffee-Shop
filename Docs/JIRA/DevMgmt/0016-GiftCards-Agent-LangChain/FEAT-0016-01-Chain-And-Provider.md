# FEAT-0016-01 — Tool-Calling Chain & Provider Binding

**Epic:** EPIC-0016 · **Depends on:** EPIC-0013 · **Blocks:** the rest of EPIC-0016

## Description

The simplest of the three agents: a bounded tool-calling chain over a small surface, bound to a provider
through the same seam.

---

## STORY-0016-01-01 — The chain

**As a** developer
**I want** a LangChain agent attached to the gift card MCP servers
**So that** it uses shared tool definitions.

### Acceptance criteria

1. **Given** the agent, **when** assembled, **then** it attaches `giftcards-mcp` and the gift cards write
   server via `langchain-mcp-adapters` and defines no tools of its own.
2. **Given** the toolset, **when** inspected at runtime, **then** it matches what those servers publish and is
   logged at startup.
3. **Given** a tool error, **when** returned, **then** the agent explains the structured reason rather than
   retrying blindly.
4. **Given** a bounded iteration limit, **when** exceeded, **then** the agent stops and explains rather than
   looping.

### Notes

The same MCP servers feed ADK in epic 0014 and LangGraph in 0015. If anything here needs a bespoke tool
definition, the MCP schema conventions failed and should be fixed there.

### Out of scope

A graph. If the flow needs one, that is a signal to revisit the framework choice, not to add nodes here.

**OpenSpec change id:** `add-giftcards-chain`

---

## STORY-0016-01-02 — Provider binding

**As an** operator
**I want** the gift card agent's model set by configuration
**So that** it uses the same seam as the other two.

### Acceptance criteria

1. **Given** `GIFTCARDS_LLM_PROVIDER`, **when** set to any supported provider, **then** the agent uses the
   native LangChain integration for it.
2. **Given** a provider not passing conformance, **when** configured, **then** the service refuses to start
   naming the finding.
3. **Given** the resolved model, **when** the service starts, **then** it is logged and on `/readyz`.

### Out of scope

Per-conversation model selection.

**OpenSpec change id:** `add-giftcards-provider-binding`

---

## STORY-0016-01-03 — Session and envelope

**As a** portal
**I want** the gift card surface to behave like the other two
**So that** one component renders all three.

### Acceptance criteria

1. **Given** a turn, **when** streamed, **then** it conforms to the published envelope and passes the
   `FEAT-0003-01` tests unchanged.
2. **Given** sessions, **when** stored, **then** they are namespaced by surface and share nothing with the
   other two.
3. **Given** a guest principal, **when** conversing, **then** the session binds to the guest identifier and
   survives migration on registration.

### Out of scope

Envelope changes.

**OpenSpec change id:** `add-giftcards-session-and-envelope`
