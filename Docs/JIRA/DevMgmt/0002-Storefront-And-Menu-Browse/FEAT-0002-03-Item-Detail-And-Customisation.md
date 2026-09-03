# FEAT-0002-03 — Item Detail & Customisation

**Epic:** EPIC-0002 · **Depends on:** FEAT-0002-02 · **Blocks:** FEAT-0002-04

## Description

Choosing size, temperature, milk and extras — and seeing the price move as you do. This screen defines the
modifier model that both the cart and the barista agent will use.

---

## STORY-0002-03-01 — Item detail view

**As a** customer
**I want** to see everything about an item before I add it
**So that** I know what I am getting and what is in it.

### Acceptance criteria

1. **Given** an item, **when** its detail view opens, **then** it shows full description, price, all allergens,
   all available modifiers, and its dietary tags.
2. **Given** Brakeman's Blend drip, **when** its detail renders, **then** the tasting notes (plum, brown sugar,
   molasses) and origin (Guatemala and Colombia) are shown.
3. **Given** an item flagged `selection_at_counter`, **when** its detail renders, **then** it states clearly
   that the specific choice is made at the case on arrival and lists the known rotation.
4. **Given** the detail view opened from the grid, **when** it is dismissed, **then** focus returns to the
   originating card and scroll position is preserved.

### Out of scope

Item photography and nutrition information.

**OpenSpec change id:** `add-item-detail-view`

---

## STORY-0002-03-02 — Modifier selection

**As a** customer
**I want** to pick size, hot or iced, milk and extras
**So that** I get the drink I actually want.

### Acceptance criteria

1. **Given** an item available hot or iced, **when** its detail renders, **then** temperature is a required
   choice with no pre-selected default, so the customer states it explicitly.
2. **Given** a milk choice, **when** I select oat or coconut, **then** the dietary badges update to reflect
   the resulting drink, and any price delta is shown against the option before I choose it.
3. **Given** a modifier that is unavailable for the chosen size or temperature, **when** that combination is
   selected, **then** the unavailable modifier is disabled with a stated reason rather than silently absent.
4. **Given** an incomplete required selection, **when** I attempt to add to cart, **then** the action is
   blocked and focus moves to the first unsatisfied choice.

### Notes

"No pre-selected default for temperature" is deliberate. Half the Brakeman's drink menu is hot-or-iced, and a
silent default is how a customer ends up with the wrong drink and the agent ends up with a bad habit to copy.

### Out of scope

Saved favourites and reorder.

**OpenSpec change id:** `add-modifier-selection`

---

## STORY-0002-03-03 — Client-side price preview

**As a** customer
**I want** the price to update as I choose options
**So that** there is no surprise at the cart.

### Acceptance criteria

1. **Given** any modifier change, **when** it is applied, **then** the displayed price updates within one
   frame and shows base price plus each modifier delta as separate lines.
2. **Given** a price preview, **when** it renders, **then** it is visibly labelled as an estimate and the
   authoritative total is stated to come from the server at cart review.
3. **Given** a quantity change, **when** applied, **then** the line total updates and quantity is bounded to
   a documented maximum with a message rather than a silent clamp.

### Notes

The preview is a convenience only. `price_order()` in `catalog-api` is authoritative — this is the same rule
that stops the barista agent from doing arithmetic, applied to the UI so both paths agree.

### Out of scope

Tax and tip.

**OpenSpec change id:** `add-price-preview`
