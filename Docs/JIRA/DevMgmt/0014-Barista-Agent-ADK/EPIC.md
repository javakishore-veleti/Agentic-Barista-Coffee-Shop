# EPIC-0014 — Barista Agent (Google ADK)

**Phase:** C · **Depends on:** EPIC-0013 · **Blocks:** nothing

## Intent

A grounded, branch-aware coffee ordering conversation on Google ADK, consuming `catalog-mcp` and the orders
write server, streaming the envelope epic 0003 already renders.

## Why ADK

The domain is a tool-calling loop over a large catalog with no required order of operations — breadth of
retrieval rather than workflow. ADK's agent and session machinery covers it with the least apparatus.

## The risk this epic carries

ADK reaches non-Gemini providers through LiteLLM, and the $0 default is a small self-hosted model. **Run the
tool-calling conformance experiment before writing agent code**, not after. If it fails, the fallback is
Gemini's free tier for this surface only.

## Success measures

- Never recommends an item the selected branch does not have today.
- Never states a price that did not come from `price_order()` in the same turn.
- Asks exactly one clarifying question when a preference is vague, including hot or iced.

## Features

| ID | Name | Stories |
|----|------|---------|
| FEAT-0014-01 | Agent, Tools & Provider Binding | 3 |
| FEAT-0014-02 | Grounding & Guardrails | 3 |
| FEAT-0014-03 | Session Memory & Branch Context | 3 |
| FEAT-0014-04 | Streaming & `ui_action` Emission | 3 |

## Out of scope

Payment, loyalty, upsell optimisation.
