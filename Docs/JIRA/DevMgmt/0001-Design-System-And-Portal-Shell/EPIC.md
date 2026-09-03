# EPIC-0001 — Design System & Portal Shell

**Phase:** A — Portals first
**Depends on:** nothing
**Blocks:** EPIC-0002, EPIC-0003, EPIC-0004

## Intent

Both portals — customer and admin — are Angular applications in one workspace, sharing a component
library and a token set. Building that shared foundation once, first, is what keeps the customer
storefront and the staff console from drifting into two different products maintained by the same team.

The brand has a visual direction to work from: railroad signal language, warm rooms, wood, no rush. The
design system should feel like a coffee shop, not like a generic SaaS admin theme — and it has to hold up
across 1,000 branches, so it carries no single location's identity.

## Why this is first

The three chat surfaces, the menu grid, and every admin table are variations on a small number of
primitives. Every day we defer the primitives is a day those variations diverge.

## Success measures

- A new screen in either portal can be built without writing a single raw colour or font-size.
- Both portals render correctly in light and dark, at 360px and at 1440px.
- Axe reports zero critical violations on every shipped route.

## Features

| ID | Name | Stories |
|----|------|---------|
| [FEAT-0001-01](FEAT-0001-01-Brand-Foundations-And-Tokens.md) | Brand Foundations & Design Tokens | 3 |
| [FEAT-0001-02](FEAT-0001-02-Angular-Workspace-And-Shell.md) | Angular Workspace & Application Shell | 4 |
| [FEAT-0001-03](FEAT-0001-03-Core-Component-Library.md) | Core Component Library | 4 |

## Out of scope for this epic

Any domain screen. No menu, no chat, no admin table. This epic delivers the vocabulary those screens
will be written in and nothing that uses it beyond a component gallery.
