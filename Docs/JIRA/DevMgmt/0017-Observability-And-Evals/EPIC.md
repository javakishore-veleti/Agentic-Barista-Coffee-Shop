# EPIC-0017 — Observability & Evals

**Phase:** C · **Depends on:** EPIC-0014, 0015, 0016 (partially deliverable earlier) · **Blocks:** EPIC-0018

## Intent

End-to-end tracing, token and cost accounting, the provider conformance suite, behavioural evals, and the
agent console in the admin portal.

## The suite that gates everything

**No provider is "supported" until it passes the tool-calling conformance suite** — parallel calls, strict
argument schemas, forced tool choice, and correct behaviour when a required parameter like `branch_id` is
missing. Ollama is in the matrix from the start, because it is the $0 default and therefore on the critical
path, not the margin.

The behavioural evals are the mock scenarios from `FEAT-0003-02`, promoted: in-menu recommendation,
out-of-menu refusal, dairy-free substitution, branch-unavailable item, the Sunday room-window quote, the
scheduled gift card delivery.

## Success measures

- One trace id follows a request from the browser through gateway, agent, MCP server and domain API.
- Cost per conversation is visible per surface and per provider.
- A regression in grounding fails a build, not a demo.

## Features

| ID | Name | Stories |
|----|------|---------|
| FEAT-0017-01 | OpenTelemetry End to End | 3 |
| FEAT-0017-02 | Token & Cost Accounting | 3 |
| FEAT-0017-03 | Provider Conformance Suite | 3 |
| FEAT-0017-04 | Behavioural Evals & Agent Console | 3 |

## Out of scope

Alerting and on-call rotation.
