# FEAT-0008-01 — Domain Model & Migrations

**Epic:** EPIC-0008 · **Depends on:** EPIC-0007 · **Blocks:** the rest of EPIC-0008

## Description

The two-level catalog: ~50 canonical brand-wide items, and the per-branch, per-date availability overlay.

---

## STORY-0008-01-01 — Canonical item model

**As a** platform
**I want** the menu as a small brand-wide set
**So that** it is edited once and embedded once.

### Acceptance criteria

1. **Given** the model, **when** inspected, **then** an item carries id, name, description, category
   (drink, bakery, retail), base price or price-by-size, tags, allergens, available milks, and a
   `selection_at_counter` flag with its known rotation options.
2. **Given** an item available hot or iced, **when** modelled, **then** temperature is a modifier with its own
   availability — not two items.
3. **Given** a modifier, **when** modelled, **then** it declares which sizes and temperatures it is valid for
   and its price delta.
4. **Given** the real menu, **when** loaded, **then** every item is representable without a special case,
   including drip with two roast choices, tea with six varieties, and retail priced as a range.
5. **Given** any item, **when** changed, **then** the change is versioned with actor and timestamp.

### Notes

`selection_at_counter` is what lets the agent honestly say a muffin is available while the specific choice
happens at the case. Without it the agent either invents today's pastry or refuses to mention pastries.

### Out of scope

Per-branch item definitions. Items are brand-wide by design — branch variation lives in availability.

**OpenSpec change id:** `add-catalog-item-model`

---

## STORY-0008-01-02 — Branch availability overlay

**As a** branch manager
**I want** availability and daily rotation stored per branch and date
**So that** what a customer is offered reflects what we have.

### Acceptance criteria

1. **Given** the model, **when** inspected, **then** availability is keyed by branch, item and effective date,
   carrying availability, an optional price override reference, and the rotation selection.
2. **Given** no explicit row for a branch, item and date, **when** resolved, **then** a documented default
   applies and the resolution states that it was a default.
3. **Given** 1,000 branches × ~50 items × 365 days, **when** the table is queried for one branch and date,
   **then** it uses an index on `(branch_id, effective_date)` and returns within a documented bound.
4. **Given** a bulk update for a group of items over a date range, **when** applied, **then** it is one
   operation rather than a row-by-row loop.

### Notes

This is the table that actually gets large — roughly 18M rows a year. It is a relational indexing problem,
not a vector one; nothing here is embedded.

### Out of scope

Stock counts and depletion.

**OpenSpec change id:** `add-branch-availability-model`

---

## STORY-0008-01-03 — Migrations and reference seed

**As a** developer
**I want** the catalog to come up populated
**So that** every later story has real data to work against.

### Acceptance criteria

1. **Given** migrations, **when** run, **then** they create the item, modifier and availability tables with
   their indexes.
2. **Given** the seed, **when** applied, **then** it loads the reference menu and a small set of branches with
   plausible availability, and is idempotent.
3. **Given** the seed, **when** compared to the portal fixture from `STORY-0002-01-03`, **then** they are
   generated from one source, so the UI and the API cannot drift.

### Notes

Criterion 3 removes a whole class of bug: the fixture the portal was built against and the seed the API
serves must not be two hand-maintained copies of the same menu.

### Out of scope

The 300k shard corpus; that is EPIC-0012.

**OpenSpec change id:** `add-catalog-migrations-and-seed`
