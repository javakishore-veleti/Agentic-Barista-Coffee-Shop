# FEAT-0007-04 — Scope Enforcement Middleware

**Epic:** EPIC-0007 · **Depends on:** FEAT-0006-01 · **Blocks:** EPIC-0008–0013

## Description

The shared component that turns `branch_scope` from a claim into a refusal. Every branch-scoped endpoint in
every domain API uses it, and so does every MCP write tool.

---

## STORY-0007-04-01 — Required branch context

**As a** platform
**I want** branch-scoped operations to demand a branch
**So that** nothing ever answers from a default.

### Acceptance criteria

1. **Given** a branch-scoped endpoint, **when** called without a branch id, **then** it returns 400 with a
   stable code naming the missing parameter — it does **not** pick one.
2. **Given** a branch id that does not exist, **when** called, **then** it returns 404, distinguishable from
   the missing-parameter case.
3. **Given** the endpoint inventory, **when** audited, **then** every branch-scoped route is registered as
   such, and a test fails if a new branch-scoped route omits the declaration.

### Notes

Criterion 3 is what keeps this true over time. Without a registry, the twentieth endpoint quietly forgets and
nobody notices until an agent answers from the wrong branch.

### Out of scope

Inferring a branch from geolocation; that is a portal concern, and it presents a choice rather than deciding.

**OpenSpec change id:** `add-required-branch-context`

---

## STORY-0007-04-02 — Scope checks

**As** head office
**I want** a branch manager to be unable to touch another branch
**So that** scope is a security control, not a UI convenience.

### Acceptance criteria

1. **Given** a principal with `branch_scope: ["br-0042"]`, **when** they read or write data for `br-0117`,
   **then** the API refuses with 403 and a stable code.
2. **Given** a principal with `branch_scope: ["*"]`, **when** they access any branch, **then** it is permitted.
3. **Given** a refusal, **when** it occurs, **then** it is written to the audit trail with actor, target branch
   and trace id.
4. **Given** the check, **when** tested, **then** the tests call the API **directly**, bypassing any portal,
   proving enforcement is not in the UI.
5. **Given** a customer principal, **when** it reaches a staff-scoped endpoint, **then** it is refused on
   realm before scope is even considered.

### Notes

Criterion 4 is the one that matters. A test that drives the admin portal proves the portal hides a button; it
proves nothing about the API.

### Out of scope

Row-level security in the database; enforcement is at the API boundary.

**OpenSpec change id:** `add-branch-scope-checks`

---

## STORY-0007-04-03 — Scope in MCP tools

**As a** platform
**I want** MCP tools to carry and enforce the caller's scope
**So that** an agent cannot exceed the authority of the person talking to it.

### Acceptance criteria

1. **Given** an MCP tool call, **when** it reaches a domain API, **then** it carries the principal of the
   customer or staff member in the conversation, not a service account.
2. **Given** a write tool, **when** called by a principal lacking scope, **then** it is refused and the agent
   receives a structured error it can explain rather than a raw failure.
3. **Given** a branch-scoped tool, **when** its schema is published, **then** `branch_id` is a **required**
   parameter, so omission is a schema error before it is a runtime one.

### Notes

This closes the loop with the read/write MCP server split: capability comes from which server the agent was
handed, authority comes from the principal it carries. Both are checked, independently.

### Out of scope

The MCP servers themselves; this story specifies the contract they must honour.

**OpenSpec change id:** `add-mcp-scope-propagation`
