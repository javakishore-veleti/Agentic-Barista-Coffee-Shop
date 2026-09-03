# FEAT-0002-04 — Cart & Order Review

**Epic:** EPIC-0002 · **Depends on:** FEAT-0002-03 · **Blocks:** EPIC-0007

## Description

Holding a customer's selections and presenting a reviewable order. The cart is also the object the barista
agent manipulates, so its model has to serve both a human clicking and an agent calling tools.

---

## STORY-0002-04-01 — Cart state

**As a** customer
**I want** my selections to persist while I browse
**So that** I do not lose my order by tapping the wrong thing.

### Acceptance criteria

1. **Given** items added to the cart, **when** I navigate between routes or reload, **then** the cart survives.
2. **Given** two identical line items with identical modifiers, **when** both are added, **then** they merge
   into one line with quantity 2 rather than appearing twice.
3. **Given** two items differing by any modifier, **when** both are added, **then** they remain separate lines.
4. **Given** a cart, **when** any line is removed, **then** an undo is offered for a bounded period before the
   removal is final.

### Notes

Cart identity is `(item_id, sorted modifier set)`. Getting this wrong is why carts elsewhere show "Latte ×1"
three times.

### Out of scope

Cross-device cart sync; there are no accounts.

**OpenSpec change id:** `add-cart-state`

---

## STORY-0002-04-02 — Order review

**As a** customer
**I want** to review the whole order with a clear total before I commit
**So that** I can correct it while it is still easy.

### Acceptance criteria

1. **Given** the review screen, **when** it renders, **then** each line shows item, every selected modifier,
   quantity, line total, and the order subtotal, with all figures in tabular numerals.
2. **Given** any line, **when** I edit it, **then** I return to that item's modifier selection pre-populated
   with my choices, not to a blank state.
3. **Given** an item that has become unavailable since it was added, **when** review renders, **then** that
   line is flagged, excluded from the total, and the reason is stated.
4. **Given** an empty cart, **when** review is reached, **then** it shows an empty state linking back to the menu.

### Out of scope

Order submission, payment, pickup time selection.

**OpenSpec change id:** `add-order-review`

---

## STORY-0002-04-03 — Cart contract for agent use

**As a** developer building the barista agent later
**I want** the cart exposed as an explicit typed contract rather than component state
**So that** the agent mutates the same cart the customer sees.

### Acceptance criteria

1. **Given** the cart, **when** inspected, **then** it is a documented typed structure with operations
   `add`, `update`, `remove`, `clear` and `snapshot`, independent of any component.
2. **Given** a cart mutation from any source, **when** it occurs, **then** every subscribed view updates
   without a manual refresh.
3. **Given** a cart snapshot, **when** serialised, **then** it round-trips exactly and is the same payload
   shape the `ui_action` order summary card will render.

### Notes

This is the seam that makes "the agent added a latte and it appeared in my cart" work without a second cart
model. Design it here, before either consumer exists, or you will end up with two.

### Out of scope

The agent itself, and the `ui_action` renderer (`FEAT-0003-03`).

**OpenSpec change id:** `add-cart-contract`
