# FEAT-0004-02 — Catalog Management

**Epic:** EPIC-0004 · **Depends on:** FEAT-0004-01 · **Blocks:** EPIC-0008

## Description

Two genuinely different jobs that a single "menu admin" screen would fuse and get wrong: editing the
brand-wide item definitions, and setting what a branch actually has today.

---

## STORY-0004-02-01 — Brand catalog editing (HQ)

**As** head office
**I want** to edit the canonical menu
**So that** every branch reflects the change without 1,000 edits.

### Acceptance criteria

1. **Given** an HQ principal, **when** the brand catalog screen loads, **then** items, descriptions, base
   prices, tags, allergens, available milks and modifier definitions are editable.
2. **Given** a non-HQ principal, **when** the screen is reached, **then** it renders read-only with the reason
   stated.
3. **Given** an edit, **when** saved, **then** it is versioned with actor and timestamp, and the previous
   version remains viewable.
4. **Given** an edit that would remove an item or modifier, **when** attempted, **then** the impact is shown —
   how many branches currently offer it and whether open orders reference it — before confirmation.
5. **Given** a saved edit, **when** it takes effect, **then** existing order lines are unaffected, because
   lines snapshot the item.

### Out of scope

Seasonal campaign scheduling.

**OpenSpec change id:** `add-brand-catalog-editing`

---

## STORY-0004-02-02 — Branch availability and daily rotation

**As a** branch manager
**I want** to set what my branch has today
**So that** customers and the barista agent are not offered things we ran out of.

### Acceptance criteria

1. **Given** a branch in my scope, **when** the availability screen loads, **then** every catalog item shows an
   availability toggle for a chosen date, defaulting to today.
2. **Given** an item flagged `selection_at_counter`, **when** I edit it, **then** I set today's actual rotation
   — which pastries are in the case, which kombucha flavours are on — from the known options list.
3. **Given** a branch outside my scope, **when** I attempt to change it, **then** the action is refused by the
   API and the UI reports the refusal rather than appearing to succeed.
4. **Given** a change, **when** saved, **then** it is effective-dated and visible to the customer portal and to
   `search_menu` without a redeploy.
5. **Given** a bulk action, **when** applied, **then** I can mark a whole group unavailable for a date range
   with one confirmation.

### Notes

Daily rotation is the highest-frequency write in the platform — a branch manager touches it every morning.
Optimise that screen for speed on a phone behind the counter, not for completeness on a desktop.

### Out of scope

Inventory counts and stock depletion.

**OpenSpec change id:** `add-branch-availability-editing`

---

## STORY-0004-02-03 — Price override requests

**As a** regional manager
**I want** to request a price override within my band and see it applied or escalated
**So that** regional pricing is a recorded decision rather than a database edit.

### Acceptance criteria

1. **Given** a proposed override inside my policy band, **when** submitted with a reason, **then** it is
   applied immediately and recorded with actor, reason and effective dates.
2. **Given** a proposed override above the band, **when** submitted, **then** it is stored as `pending`,
   surfaced to HQ for approval, and not applied until approved.
3. **Given** any override, **when** viewed, **then** its full history — who proposed, who approved, when it
   takes effect and expires — is visible on one screen.
4. **Given** an active override, **when** the customer portal or `search_menu` returns that item for that
   branch, **then** the overridden price is used.

### Out of scope

Promotional campaigns and discount codes; those are order-level, in EPIC-0009.

**OpenSpec change id:** `add-price-override-requests`
