# EPIC-0013 — MCP Tool Layer

**Phase:** C · **Depends on:** EPIC-0008, 0009, 0010, 0011 · **Blocks:** EPIC-0014, 0015, 0016

## Intent

One MCP server per domain, consumed by all three agent frameworks. Plus `ops-mcp`, which makes index health
and reindexing available to the team from Claude Code.

## Why this is an epic and not plumbing

1. **One definition.** Without it, `search_menu` is written three times — an ADK function tool, a LangChain
   `@tool`, and again for LangGraph — and the three drift.
2. **A real security boundary.** Read tools and write tools live on **separate servers**, so an agent given
   only the read server physically cannot call `issue_card`. No prompt can talk it out of a capability it was
   never handed.
3. **Reach beyond our own agents.** `catalog-mcp` is usable by anything that speaks MCP.

## The rule that must not bend

**An MCP server is a façade over the domain API's HTTP endpoints, never over the database.** If a tool needs
data the API does not expose, the fix is an endpoint.

## Success measures

- The same server, unchanged, is consumed by ADK and by LangChain.
- `branch_id` is required on every branch-scoped tool, and omitting it fails loudly.
- An agent carries the caller's principal; the write server refuses a principal without scope.

## Features

| ID | Name | Stories |
|----|------|---------|
| FEAT-0013-01 | MCP Server Scaffold & Transport | 3 |
| FEAT-0013-02 | Read Servers (catalog, events, giftcards) | 3 |
| FEAT-0013-03 | Write Servers & Authority Checks | 3 |
| FEAT-0013-04 | `ops-mcp` for the Team | 3 |

## Out of scope

Any agent. This epic ships tools and their tests.
