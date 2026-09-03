# FEAT-0015-01 — Booking Graph & Nodes

**Epic:** EPIC-0015 · **Depends on:** EPIC-0013 · **Blocks:** the rest of EPIC-0015

## Description

The graph itself: explicit nodes, explicit transitions, explicit state. This is where LangGraph earns its
place over a tool loop.

---

## STORY-0015-01-01 — Graph definition

**As a** developer
**I want** the booking flow as a graph with named nodes
**So that** the conversation's structure is inspectable rather than emergent.

### Acceptance criteria

1. **Given** the graph, **when** inspected, **then** it defines nodes: collect requirements, check
   availability, quote, confirm details, await approval, hold, submit, and terminal success and failure.
2. **Given** the state schema, **when** inspected, **then** it holds branch, requirements, candidate slots,
   quote, hold reference, inquiry reference and the decision — typed, not free-form.
3. **Given** any transition, **when** it occurs, **then** it is driven by state, not by parsing the model's
   prose.
4. **Given** the graph, **when** rendered, **then** a diagram can be produced from the definition for
   documentation.

### Notes

Criterion 3 is the discipline that makes checkpointing meaningful. A graph whose edges depend on interpreting
free text is a tool loop wearing a graph's clothes.

### Out of scope

Sub-graphs for catering or equipment.

**OpenSpec change id:** `add-booking-graph-definition`

---

## STORY-0015-01-02 — Requirement collection

**As a** customer
**I want** to be asked for what is actually needed
**So that** my inquiry is complete first time.

### Acceptance criteria

1. **Given** the collect node, **when** active, **then** it gathers every field the inquiry requires: names,
   email, phone, event date, alternate date, event type, desired start and length, guest count and bar type.
2. **Given** partial information across turns, **when** provided, **then** state accumulates and only missing
   fields are asked for.
3. **Given** a guest count above the branch tier's capacity, **when** provided, **then** the node reports the
   limit immediately rather than proceeding to quote.
4. **Given** a field the customer declines to give, **when** it is required, **then** the agent explains why it
   is needed rather than looping.

### Out of scope

Free-form requirements capture beyond the defined fields.

**OpenSpec change id:** `add-requirement-collection-node`

---

## STORY-0015-01-03 — Availability and quoting nodes

**As a** customer
**I want** real dates and real prices
**So that** the conversation is worth having.

### Acceptance criteria

1. **Given** requirements, **when** the availability node runs, **then** it calls `find_slots` and stores real
   slots in state — the agent never asserts availability itself.
2. **Given** no availability, **when** it occurs, **then** the graph moves to a branch offering the alternate
   date rather than terminating.
3. **Given** a chosen slot, **when** the quote node runs, **then** it calls `quote` and stores the quote,
   including the **room window** and the expiry.
4. **Given** an expired quote on resume, **when** detected, **then** the graph routes to re-quote rather than
   presenting a stale price.

### Out of scope

Negotiating price.

**OpenSpec change id:** `add-availability-quote-nodes`
