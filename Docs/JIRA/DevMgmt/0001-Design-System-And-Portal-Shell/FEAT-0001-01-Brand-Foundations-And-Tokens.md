# FEAT-0001-01 — Brand Foundations & Design Tokens

**Epic:** EPIC-0001 · **Depends on:** nothing · **Blocks:** FEAT-0001-02, FEAT-0001-03

## Description

A single token layer that every component and both portals consume. Tokens are CSS custom properties
so that theme switching costs nothing at runtime and so that non-Angular surfaces (a future email
template, a static page) can adopt the same palette.

The palette derives from the shop: signal green, oxide red, roasted browns, and a cool paper neutral —
not a default framework theme.

---

## STORY-0001-01-01 — Define the token set

**As a** developer building any Brakeman's screen
**I want** a documented set of colour, type, spacing and elevation tokens
**So that** I never choose a hex value or a pixel size by hand.

### Acceptance criteria

1. **Given** the token stylesheet is imported, **when** I inspect `:root`, **then** every colour used
   anywhere in either portal is available as a custom property, and no component stylesheet contains a
   literal hex value.
2. **Given** the token set, **when** I look for a semantic colour, **then** `--status-success`,
   `--status-warning`, `--status-danger` and `--status-info` exist and are distinct from the brand accent.
3. **Given** the type scale, **when** I set any text, **then** the size comes from a named step
   (`--type-xs` through `--type-3xl`) and the scale has no more than eight steps.
4. **Given** the spacing scale, **when** I set any gap or padding, **then** it comes from a 4px-based
   named step.

### Notes

Semantic status colours are deliberately separate from the accent. Admin tables will encode booking
state and index health with them, and an accent that doubles as "success" makes those tables unreadable.

### Out of scope

Component styles. This story ships tokens and a documentation page, nothing that consumes them.

**OpenSpec change id:** `add-design-tokens`

---

## STORY-0001-01-02 — Light and dark themes

**As a** customer using my phone at 6am or 6pm
**I want** the site to match my system appearance
**So that** it is comfortable to read in the shop and on the walk over.

### Acceptance criteria

1. **Given** no explicit preference, **when** the OS is set to dark, **then** the portals render dark;
   when set to light, they render light.
2. **Given** an explicit in-app theme choice, **when** it is set, **then** it overrides the OS preference
   in both directions and persists across reloads.
3. **Given** either theme, **when** any token pair is checked, **then** body text meets WCAG AA against
   its surface and large text meets AA at minimum.
4. **Given** a theme switch, **when** it happens, **then** no component re-mounts and no flash of
   unstyled content occurs.

### Notes

Every token must be declared in the base `:root` block before any media query or theme attribute
redefines it. A colour whose only definition sits inside a dark-mode block renders as nothing in the
un-stamped system state — this is the single most common bug in this area.

### Out of scope

A theme toggle control in the shell; that is `STORY-0001-02-04`.

**OpenSpec change id:** `add-light-dark-theming`

---

## STORY-0001-01-03 — Typography and iconography

**As a** visitor
**I want** the site to read like Brakeman's
**So that** it feels like the shop I walk into rather than a template.

### Acceptance criteria

1. **Given** the app loads, **when** fonts are requested, **then** a display face and a body face are
   loaded with `display=swap` and each declares a real system fallback stack.
2. **Given** a slow connection, **when** fonts have not arrived, **then** text is readable in the
   fallback and does not reflow more than one line on arrival.
3. **Given** any icon in the system, **when** it renders, **then** it is an inline SVG inheriting
   `currentColor`, and no icon font is loaded.
4. **Given** an icon used without a visible label, **when** a screen reader encounters it, **then** it
   carries an accessible name.

### Out of scope

Illustration and photography direction.

**OpenSpec change id:** `add-typography-and-icons`
