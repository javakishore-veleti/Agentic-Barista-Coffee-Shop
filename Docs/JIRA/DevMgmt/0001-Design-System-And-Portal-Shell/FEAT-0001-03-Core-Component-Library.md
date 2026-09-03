# FEAT-0001-03 — Core Component Library

**Epic:** EPIC-0001 · **Depends on:** FEAT-0001-01 · **Blocks:** EPIC-0002, EPIC-0003, EPIC-0004

## Description

The primitives every later screen composes from. Scope is deliberately narrow: build what 0002–0004
provably need and nothing speculative.

---

## STORY-0001-03-01 — Form and action primitives

**As a** developer building any form
**I want** buttons, inputs, selects, and field wrappers that handle validation display
**So that** every form in the platform behaves and looks the same.

### Acceptance criteria

1. **Given** any interactive primitive, **when** focused by keyboard, **then** it shows a visible focus
   ring that meets contrast requirements in both themes.
2. **Given** a field in an error state, **when** it renders, **then** the message is programmatically
   associated with the input and error is conveyed by text, not colour alone.
3. **Given** a button performing an async action, **when** it is pending, **then** it is disabled, shows
   pending state, and cannot double-submit.
4. **Given** any primitive, **when** rendered at 200% browser zoom, **then** no content is clipped or
   overlapped.

### Out of scope

Complex composite widgets — date range pickers, rich text. Those arrive with the screens that need them.

**OpenSpec change id:** `add-form-primitives`

---

## STORY-0001-03-02 — Data display primitives

**As a** developer building admin screens
**I want** a table, a card, a badge and a definition list
**So that** catalog, inquiry and ledger views share one visual language.

### Acceptance criteria

1. **Given** a table wider than its container, **when** it renders, **then** it scrolls horizontally
   within its own container and the page body never scrolls sideways.
2. **Given** a table of numeric columns, **when** it renders, **then** digits are set with tabular
   figures and right-aligned.
3. **Given** a badge, **when** it conveys status, **then** it carries a text label in addition to colour
   and maps to the semantic status tokens.
4. **Given** a card, **when** several appear in a row, **then** their inner padding, radius and edges are
   identical and content of differing length does not distort the row.

### Out of scope

Sorting, pagination and filtering behaviour; those are table *features* delivered per screen.

**OpenSpec change id:** `add-data-display-primitives`

---

## STORY-0001-03-03 — Overlay primitives

**As a** developer
**I want** a dialog, a drawer and a toast
**So that** confirmation and notification behave consistently and accessibly.

### Acceptance criteria

1. **Given** an open dialog, **when** it has focus, **then** focus is trapped inside it, Escape closes it,
   and focus returns to the trigger on close.
2. **Given** an open overlay, **when** a screen reader is active, **then** background content is marked
   inert.
3. **Given** a toast, **when** it appears, **then** it is announced politely, is dismissible, and stacks
   predictably when several occur.
4. **Given** any overlay, **when** the viewport is under 768px, **then** it uses a layout appropriate to
   the width rather than a shrunken desktop dialog.

### Notes

No native `alert()`, `confirm()` or `prompt()` anywhere in either portal, ever.

### Out of scope

Toast persistence across route changes.

**OpenSpec change id:** `add-overlay-primitives`

---

## STORY-0001-03-04 — Component gallery

**As a** developer or designer
**I want** a route that renders every primitive in every state
**So that** I can see the system whole and catch regressions visually.

### Acceptance criteria

1. **Given** the gallery route, **when** it loads, **then** every primitive appears in default, hover,
   focus, disabled, error and loading states where applicable.
2. **Given** the gallery, **when** the theme changes, **then** every example re-renders correctly in the
   new theme with no hardcoded colours surviving.
3. **Given** a production build of the customer portal, **when** it is built, **then** the gallery route
   is excluded from the bundle.

### Out of scope

Storybook or any third-party gallery tooling — this is a route in the workspace.

**OpenSpec change id:** `add-component-gallery`
