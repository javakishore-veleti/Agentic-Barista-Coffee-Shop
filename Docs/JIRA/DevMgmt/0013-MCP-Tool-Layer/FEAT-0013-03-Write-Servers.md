# FEAT-0013-03 — Write Servers & Authority Checks

**Epic:** EPIC-0013 · **Depends on:** FEAT-0013-02 · **Blocks:** EPIC-0014, 0015, 0016

## Description

Separate servers for every mutating tool. This split is the security boundary the whole agent design rests on.

---

## STORY-0013-03-01 — The read/write split

**As a** platform
**I want** write tools on their own servers
**So that** a read-only agent physically cannot mutate anything.

### Acceptance criteria

1. **Given** the servers, **when** enumerated, **then** no mutating tool appears on any read server.
2. **Given** an agent configured with read servers only, **when** it attempts a write, **then** the tool does
   not exist in its toolset — the failure is absence of capability, not a refusal.
3. **Given** the configuration, **when** an agent is assembled, **then** which servers it receives is explicit
   and reviewable, not implied.
4. **Given** a test, **when** run, **then** it asserts that a read-only agent's published toolset contains no
   write tool.

### Notes

The distinction in criterion 2 is the point. A refusal is something a prompt can argue with; an absent
capability is not.

### Out of scope

Fine-grained per-tool permissions within a server.

**OpenSpec change id:** `add-mcp-read-write-split`

---

## STORY-0013-03-02 — Order and event write tools

**As** an agent
**I want** to build carts, submit orders and hold slots
**So that** a conversation can complete a transaction.

### Acceptance criteria

1. **Given** the orders write server, **when** listed, **then** it exposes `add_to_cart`, `update_cart`,
   `submit_order(idempotency_key, branch_id)` and returns the priced document.
2. **Given** the events write server, **when** listed, **then** it exposes `hold_slot(branch_id, slot, ttl)`
   and `submit_inquiry(...)` requiring every field the inquiry demands.
3. **Given** any write tool, **when** called, **then** it requires an idempotency key where repetition would
   otherwise duplicate.
4. **Given** a write refused on scope or availability, **when** it returns, **then** the error is structured
   enough for the agent to explain precisely what failed.
5. **Given** `submit_order`, **when** called, **then** it never computes a total — it submits and returns what
   the API priced.

### Out of scope

Cancellation and amendment tools.

**OpenSpec change id:** `add-order-event-write-tools`

---

## STORY-0013-03-03 — Gift card write tools

**As** an agent
**I want** to issue and reload cards
**So that** a purchase can complete in conversation.

### Acceptance criteria

1. **Given** the gift cards write server, **when** listed, **then** it exposes `issue_card(...)` and
   `reload_card(card_ref, amount)`, both requiring an idempotency key.
2. **Given** `issue_card`, **when** called by a guest principal, **then** it succeeds and associates the card
   with the guest identifier.
3. **Given** any write, **when** it completes, **then** it returns the resulting ledger state so the agent
   never has to infer a balance.
4. **Given** a failure between payment and issuance, **when** it occurs, **then** the tool returns a structured
   error stating no card was created and no money captured.

### Notes

Criterion 3 is what makes the money-safety guardrail achievable: the agent is always handed the new balance,
so it never has any reason to compute one.

### Out of scope

Refunds and cancellations.

**OpenSpec change id:** `add-giftcard-write-tools`
