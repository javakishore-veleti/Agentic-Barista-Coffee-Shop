# FEAT-0013-04 — `ops-mcp` for the Team

**Epic:** EPIC-0013 · **Depends on:** FEAT-0013-01, FEAT-0012-04 · **Blocks:** nothing

## Description

An MCP server aimed at the development team rather than at customers — index health, reindexing, shard
statistics and resolving a stuck booking, reachable from Claude Code.

---

## STORY-0013-04-01 — Operational read tools

**As a** developer
**I want** to ask about system state in plain English
**So that** I am not writing ad-hoc SQL to answer routine questions.

### Acceptance criteria

1. **Given** the server, **when** listed, **then** it exposes `index_status()`, `shard_stats(domain)` and
   `config_snapshot(service)`.
2. **Given** `config_snapshot`, **when** called, **then** it returns the resolved provider, embedding triple,
   store mode and auth provider for a service, with secrets redacted.
3. **Given** any read tool, **when** called, **then** it is safe — it changes nothing.

### Notes

This is the same information `docker-all-status.sh` prints, reachable conversationally. Both exist because the
question gets asked constantly once three providers and three store modes are live.

### Out of scope

Log search and metric queries.

**OpenSpec change id:** `add-ops-mcp-read-tools`

---

## STORY-0013-04-02 — Operational write tools

**As a** developer
**I want** to trigger a reindex or unstick a booking
**So that** routine recovery does not need a shell.

### Acceptance criteria

1. **Given** the server, **when** listed, **then** write tools — `reindex_shards(domain)` and
   `resolve_interrupt(booking_ref, decision)` — are on a **separate** server from the read tools.
2. **Given** a write tool, **when** called, **then** it requires a staff principal with sufficient role and
   refuses otherwise.
3. **Given** `reindex_shards`, **when** a run is already in progress, **then** it is refused rather than
   starting a second writer.
4. **Given** any operational write, **when** performed, **then** it is written to the audit trail with the
   acting principal.

### Notes

The read/write split applies here too, and matters more: these tools act on the platform itself.

### Out of scope

Destructive operations — dropping indexes, deleting data — which stay out of MCP entirely.

**OpenSpec change id:** `add-ops-mcp-write-tools`

---

## STORY-0013-04-03 — Local connection recipe

**As a** developer
**I want** a documented way to connect Claude Code to `ops-mcp`
**So that** the capability is actually used.

### Acceptance criteria

1. **Given** the documentation, **when** followed, **then** a developer connects Claude Code to `ops-mcp` over
   stdio against a running local stack in under five minutes.
2. **Given** the recipe, **when** followed, **then** it uses a seeded staff principal and states plainly that
   the write server acts as that user.
3. **Given** the connection, **when** established, **then** asking about index health returns real data from
   the running stack.

### Out of scope

Connecting to a deployed environment; that requires the OIDC path from EPIC-0018.

**OpenSpec change id:** `add-ops-mcp-connection-docs`
