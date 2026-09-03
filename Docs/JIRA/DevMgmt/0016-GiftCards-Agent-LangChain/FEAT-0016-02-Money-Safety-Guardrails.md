# FEAT-0016-02 — Money-Safety Guardrails

**Epic:** EPIC-0016 · **Depends on:** FEAT-0016-01 · **Blocks:** EPIC-0017 evals

## Description

The guardrail that defines this epic: **the agent may not state a monetary amount that did not come back from
a tool call in the current turn.** Enforced by a check, not by asking the prompt nicely.

---

## STORY-0016-02-01 — Amount provenance check

**As a** customer
**I want** every figure the agent states to be real
**So that** I am not told a balance that does not exist.

### Acceptance criteria

1. **Given** a response containing a monetary figure, **when** the guardrail runs, **then** that figure must
   appear in a tool result from the **current** turn, or the response is blocked and regenerated.
2. **Given** a figure from an earlier turn, **when** restated, **then** it is only permitted if the agent
   re-reads it in this turn — a balance is never quoted from memory.
3. **Given** a blocked response, **when** it happens, **then** it is logged with the offending figure so the
   failure mode is measurable.
4. **Given** the check, **when** implemented, **then** it recognises figures written as words and as digits,
   and its coverage is tested.

### Notes

Balances move. A card read five minutes ago may have been spent since, and a model restating a remembered
figure is the specific way this domain produces confident falsehoods.

### Out of scope

Verifying non-monetary claims; those are covered by domain-specific grounding.

**OpenSpec change id:** `add-amount-provenance-guardrail`

---

## STORY-0016-02-02 — Capability restriction

**As a** platform
**I want** a read-only configuration of this agent to be genuinely read-only
**So that** the MCP split is demonstrated, not just described.

### Acceptance criteria

1. **Given** a read-only configuration, **when** the agent is assembled, **then** its toolset contains no write
   tool — the capability is absent, not refused.
2. **Given** a read-only agent, **when** a user asks it to issue a card, **then** it explains it cannot and
   directs them to the purchase flow.
3. **Given** a test, **when** run, **then** it asserts the published toolset of the read-only configuration
   contains no mutating tool.
4. **Given** a customer principal, **when** it reaches a write tool, **then** authority is checked
   independently of capability, and both must pass.

### Notes

This is the epic where the read/write server split gets its clearest test, because the write tools here move
money.

### Out of scope

Per-tool dynamic permissions.

**OpenSpec change id:** `add-giftcards-capability-restriction`

---

## STORY-0016-02-03 — Confirmation before writes

**As a** customer
**I want** to confirm before money moves
**So that** a misunderstanding does not cost me.

### Acceptance criteria

1. **Given** an intent to purchase or reload, **when** the agent is ready to act, **then** it emits a
   `giftcard_preview` card and requires an explicit confirmation before calling any write tool.
2. **Given** confirmation, **when** given, **then** the write is called with an idempotency key derived from
   the confirmed intent.
3. **Given** an ambiguous confirmation, **when** received, **then** the agent asks again rather than proceeding.
4. **Given** a repeated confirmation for the same intent, **when** it occurs, **then** idempotency prevents a
   second card.

### Out of scope

Multi-factor confirmation for high-value cards.

**OpenSpec change id:** `add-giftcards-write-confirmation`
