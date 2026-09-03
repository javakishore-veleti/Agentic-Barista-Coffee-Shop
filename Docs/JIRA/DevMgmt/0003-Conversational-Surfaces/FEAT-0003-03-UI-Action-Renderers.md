# FEAT-0003-03 — Rich `ui_action` Renderers

**Epic:** EPIC-0003 · **Depends on:** FEAT-0003-02, FEAT-0002-04 · **Blocks:** FEAT-0003-04

## Description

`ui_action` is how an agent pushes a real component into the transcript instead of drawing a table in text.
It is also the mechanism that keeps the model out of formatting money.

---

## STORY-0003-03-01 — Order summary card

**As a** customer ordering by chat
**I want** my order shown as a proper summary with a real total
**So that** I can check it as easily as I could on the cart screen.

### Acceptance criteria

1. **Given** a `ui_action` of kind `order_summary`, **when** rendered, **then** it shows each line with its
   modifiers, quantity and line total, plus subtotal, each discount, each tax line, and the grand total.
2. **Given** the card, **when** it renders figures, **then** every figure comes from the payload and none is
   parsed out of the assistant's prose.
3. **Given** the card, **when** it renders, **then** it offers "edit in cart", which opens the same cart the
   customer would have built by clicking, populated identically.
4. **Given** a payload failing schema validation, **when** received, **then** a compact fallback renders and an
   error is logged, rather than a broken card or a thrown exception in the transcript.

### Notes

This card and the cart screen share the cart contract from `STORY-0002-04-03`. There is one cart, and the
chat is a second way to reach it.

### Out of scope

Editing quantities inside the card itself.

**OpenSpec change id:** `add-order-summary-card`

---

## STORY-0003-03-02 — Event quote card

**As a** customer booking the room
**I want** the quote shown with the actual time the room is held
**So that** I understand what I am paying for.

### Acceptance criteria

1. **Given** a `ui_action` of kind `event_quote`, **when** rendered, **then** it shows the branch, date, event
   window, **and the full room window including setup and cleanup**, drawn as a single timeline.
2. **Given** a 2-hour Sunday booking starting at 7pm, **when** rendered, **then** the room window shows
   6:00pm–9:30pm and the event window 7:00pm–9:00pm, visually distinguished.
3. **Given** the quote, **when** rendered, **then** it shows the rate, the branch's tier-derived rate card
   basis, guest capacity, and what is included.
4. **Given** a quote that has expired or whose hold has lapsed, **when** rendered, **then** it is visibly stale
   and offers to re-quote rather than showing a price that is no longer available.

### Out of scope

Calendar export.

**OpenSpec change id:** `add-event-quote-card`

---

## STORY-0003-03-03 — Gift card preview

**As a** customer buying a gift card
**I want** to see exactly what the recipient will get and when
**So that** I do not send the wrong thing to the wrong person.

### Acceptance criteria

1. **Given** a `ui_action` of kind `giftcard_preview`, **when** rendered, **then** it shows amount, recipient,
   delivery channel, delivery date and time, and the personal message.
2. **Given** a scheduled delivery, **when** rendered, **then** the date and time are shown in the recipient's
   stated timezone with the timezone named explicitly.
3. **Given** a group card, **when** rendered, **then** contributors and the pooled total are shown distinctly
   from a single-purchaser card.
4. **Given** any amount displayed, **when** rendered, **then** it comes from the payload; the renderer performs
   no arithmetic of its own.

### Out of scope

Card artwork selection.

**OpenSpec change id:** `add-giftcard-preview-card`
