# FEAT-0013-01 — MCP Server Scaffold & Transport

**Epic:** EPIC-0013 · **Depends on:** EPIC-0008–0011 · **Blocks:** the rest of EPIC-0013

## Description

The shared scaffold every MCP server uses: transport, principal propagation, error mapping, and the schema
conventions that make one definition serve three frameworks.

---

## STORY-0013-01-01 — Server scaffold

**As a** developer building an MCP server
**I want** transport, logging, tracing and error mapping provided
**So that** four servers do not each invent them.

### Acceptance criteria

1. **Given** the scaffold, **when** used, **then** a server supports **stdio** for local use and **streamable
   HTTP** when deployed, selected by configuration.
2. **Given** a tool call, **when** it fails, **then** the domain API's error envelope is mapped to a structured
   MCP error the agent can explain — not a stack trace and not a bare string.
3. **Given** a tool call, **when** it occurs, **then** the trace id is propagated to the domain API and the
   call is recorded as a span.
4. **Given** a server, **when** started, **then** it reports which domain API it fronts and refuses to start if
   that API is unreachable.

### Notes

Stdio matters beyond development: it is what lets the team point Claude Code or Claude Desktop at `ops-mcp`
and ask about index health in plain English.

### Out of scope

Any tool definition; this story ships the frame.

**OpenSpec change id:** `add-mcp-server-scaffold`

---

## STORY-0013-01-02 — Tool schema conventions

**As a** developer
**I want** schema rules that work across ADK, LangChain and LangGraph
**So that** one definition genuinely serves all three.

### Acceptance criteria

1. **Given** any tool, **when** its schema is published, **then** parameters are flat, typed and
   individually described — no deeply nested objects, which small models handle poorly.
2. **Given** a branch-scoped tool, **when** its schema is published, **then** `branch_id` is a **required**
   parameter, so omission is a schema error before it is a runtime one.
3. **Given** a tool description, **when** written, **then** it states what the tool returns and when to use it,
   in language aimed at a model rather than a developer.
4. **Given** a schema change, **when** it is breaking, **then** the tool is versioned rather than mutated.
5. **Given** the schemas, **when** validated, **then** a test asserts every published tool conforms to these
   conventions.

### Notes

Flat parameters is not stylistic. Under the $1 posture the default model is small, and nested argument objects
are where small models most reliably fail.

### Out of scope

Prompt engineering for individual agents.

**OpenSpec change id:** `add-mcp-schema-conventions`

---

## STORY-0013-01-03 — Principal propagation

**As a** platform
**I want** the caller's identity carried into every tool call
**So that** an agent never exceeds the authority of the person talking to it.

### Acceptance criteria

1. **Given** a tool call, **when** it reaches the domain API, **then** it carries the principal of the customer
   or staff member in the conversation — **never** a service account.
2. **Given** a call with no principal, **when** received, **then** it is refused rather than defaulting to
   anonymous or to a system identity.
3. **Given** a refusal on scope, **when** returned to the agent, **then** it is a structured error the agent can
   explain to the user, not a generic failure.
4. **Given** the propagation, **when** tested, **then** tests prove a scoped principal is refused, calling the
   MCP server directly rather than through an agent.

### Out of scope

Delegation and on-behalf-of flows.

**OpenSpec change id:** `add-mcp-principal-propagation`
