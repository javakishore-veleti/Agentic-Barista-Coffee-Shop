# FEAT-0014-02 — Grounding & Guardrails

**Epic:** EPIC-0014 · **Depends on:** FEAT-0014-01 · **Blocks:** EPIC-0017 evals

## Description

The behaviour that makes the agent trustworthy — enforced by checks, not only by instruction.

---

## STORY-0014-02-01 — Catalog grounding

**As a** customer
**I want** the barista to only offer things we actually have
**So that** I am not promised a drink that does not exist.

### Acceptance criteria

1. **Given** any recommendation, **when** produced, **then** every item named appears in a `search_menu` or
   `get_item` result from the current turn.
2. **Given** a request for something not on the menu, **when** made, **then** the agent declines and says so
   plainly, offering the nearest on-menu alternative.
3. **Given** an item on the brand menu but unavailable at that branch today, **when** asked for, **then** the
   agent says it is not available **here today** — not that it does not exist.
4. **Given** a response naming an item not present in any tool result that turn, **when** the guardrail runs,
   **then** the response is blocked and regenerated rather than sent.

### Notes

Criterion 4 is the difference between a prompt instruction and a guarantee. The check is cheap: extract item
names from the response and intersect with the turn's tool results.

### Out of scope

Suggesting items from another branch.

**OpenSpec change id:** `add-barista-catalog-grounding`

---

## STORY-0014-02-02 — Dietary and allergen safety

**As a** customer with a dietary need
**I want** advice I can rely on
**So that** I am not made ill by a confident answer.

### Acceptance criteria

1. **Given** a stated dietary need, **when** recommending, **then** the agent uses the dietary filter rather
   than reasoning about allergens from item descriptions.
2. **Given** an item eligible only via a substitution, **when** recommended, **then** the agent names the
   required modifier explicitly — for example oat milk — and includes it in the cart.
3. **Given** an allergen question, **when** answered, **then** the answer comes from the item's allergen data,
   and the agent does not speculate about traces or preparation.
4. **Given** uncertainty, **when** it arises, **then** the agent says what it does not know and suggests asking
   staff, rather than reassuring.

### Notes

Criterion 3 is a hard boundary. Allergen data is a list, not an inference; a model that reasons about what
"probably" contains dairy is a liability.

### Out of scope

Cross-contamination and preparation-surface guidance.

**OpenSpec change id:** `add-barista-dietary-safety`

---

## STORY-0014-02-03 — One clarifying question

**As a** customer with a vague request
**I want** one useful question, not an interrogation
**So that** ordering stays fast.

### Acceptance criteria

1. **Given** a vague preference, **when** the agent responds, **then** it asks exactly **one** clarifying
   question, chosen to narrow the most.
2. **Given** a hot-or-iced item, **when** temperature has not been stated, **then** it is asked and never
   defaulted.
3. **Given** an answer to the clarifying question, **when** received, **then** the agent proceeds to a
   recommendation rather than asking again.
4. **Given** a request already specific enough, **when** received, **then** no clarifying question is asked.

### Out of scope

Multi-turn preference elicitation and profiling.

**OpenSpec change id:** `add-barista-clarifying-question`
