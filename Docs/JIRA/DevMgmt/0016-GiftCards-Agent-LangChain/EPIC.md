# EPIC-0016 — Gift Cards Agent (LangChain)

**Phase:** C · **Depends on:** EPIC-0013 · **Blocks:** nothing

## Intent

Purchase, reload and balance by conversation — a bounded tool-calling chain over a small surface.

## Why LangChain

It is the least framework that does the job, and having it beside a graph and an ADK agent in one repo makes
the contrast legible.

## The guardrail that defines this epic

**The agent may not state a monetary amount that did not come back from a tool call in the current turn.**
Not a balance, not a total, not a remaining value. This is enforced by a response check, not by asking the
prompt nicely.

## Success measures

- Every figure in every transcript traces to a ledger or quote call in the same turn.
- A read-only configuration of the agent provably cannot issue a card.
- Scheduled delivery states the recipient's timezone explicitly.

## Features

| ID | Name | Stories |
|----|------|---------|
| FEAT-0016-01 | Tool-Calling Chain & Provider Binding | 3 |
| FEAT-0016-02 | Money-Safety Guardrails | 3 |
| FEAT-0016-03 | Purchase, Reload & Balance Flows | 3 |

## Out of scope

Real payment capture, dispute handling.
