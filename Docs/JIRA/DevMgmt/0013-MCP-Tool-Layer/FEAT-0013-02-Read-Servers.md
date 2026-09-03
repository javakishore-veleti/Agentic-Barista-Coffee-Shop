# FEAT-0013-02 — Read Servers

**Epic:** EPIC-0013 · **Depends on:** FEAT-0013-01 · **Blocks:** EPIC-0014, 0015, 0016

## Description

`catalog-mcp`, `events-mcp` and `giftcards-mcp` — read-only tools, safe to hand to any agent.

---

## STORY-0013-02-01 — catalog-mcp

**As** an agent
**I want** to search and read the menu
**So that** every recommendation is grounded.

### Acceptance criteria

1. **Given** the server, **when** its tools are listed, **then** it exposes `search_menu(query, branch_id,
   filters)`, `get_item(item_id, branch_id)` and `price_order(cart, branch_id)`.
2. **Given** `search_menu`, **when** called, **then** it returns only items available at that branch today,
   with the substitution case distinguished from the natively-compliant case.
3. **Given** a query matching an item unavailable at that branch, **when** called, **then** the response
   distinguishes "not on the menu" from "on the menu, unavailable here today".
4. **Given** `price_order`, **when** called, **then** it returns the priced document from `catalog-api` and
   performs no arithmetic of its own.
5. **Given** any tool, **when** called without `branch_id`, **then** it fails on schema.

### Notes

Criterion 3 is what lets the barista say the honest thing rather than a false denial.

### Out of scope

Writes of any kind. `price_order` is pure.

**OpenSpec change id:** `add-catalog-mcp-read-server`

---

## STORY-0013-02-02 — events-mcp

**As** an agent
**I want** to find slots and quote events
**So that** availability and price are never invented.

### Acceptance criteria

1. **Given** the server, **when** its tools are listed, **then** it exposes `find_slots(branch_id, date_range,
   duration, guests)` and `quote(branch_id, date, start, duration, event_type)`.
2. **Given** `find_slots`, **when** it returns a slot, **then** the slot states both the event window and the
   **room window** including setup and cleanup.
3. **Given** `quote`, **when** called, **then** it returns price, capacity, inclusions, tier basis, room window
   and an expiry.
4. **Given** an ineligible event type for that branch's tier, **when** quoted, **then** a structured reason is
   returned rather than empty availability.

### Out of scope

Holds and submission; those are write tools.

**OpenSpec change id:** `add-events-mcp-read-server`

---

## STORY-0013-02-03 — giftcards-mcp

**As** an agent
**I want** to read denominations and balances
**So that** every figure I state came from a tool.

### Acceptance criteria

1. **Given** the server, **when** its tools are listed, **then** it exposes `list_denominations(branch_id)`,
   `quote_card(amount, delivery)` and `check_balance(card_ref)`.
2. **Given** `check_balance`, **when** called by a principal without rights to that card, **then** it is
   refused.
3. **Given** any monetary value returned, **when** returned, **then** it carries the currency and the timestamp
   it was computed at.
4. **Given** `quote_card`, **when** called, **then** it validates the amount against configured bounds and
   returns a refusal naming the bound if outside.

### Out of scope

Issue, reload and redeem; those are write tools.

**OpenSpec change id:** `add-giftcards-mcp-read-server`
