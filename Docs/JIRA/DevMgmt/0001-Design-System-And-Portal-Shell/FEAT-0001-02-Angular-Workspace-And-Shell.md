# FEAT-0001-02 — Angular Workspace & Application Shell

**Epic:** EPIC-0001 · **Depends on:** FEAT-0001-01 · **Blocks:** EPIC-0002, EPIC-0004

## Description

One Angular workspace containing two applications and one shared library. The shell is the frame both
applications live inside: header, navigation, routing, error and loading states.

---

## STORY-0001-02-01 — Workspace scaffold

**As a** developer
**I want** `Portals/CoffeeShop` and `Portals/CoffeeShopAdmin` in one Angular workspace with a shared library
**So that** components and tokens are shared by import rather than by copy.

### Acceptance criteria

1. **Given** the workspace, **when** I run the build, **then** both applications build independently and
   the shared library builds once.
2. **Given** the shared library, **when** either app imports from it, **then** the import path is a named
   alias, not a relative traversal out of the app folder.
3. **Given** either app, **when** I run it, **then** it starts on its documented port (4200 customer,
   4201 admin) and reads its API base URL from environment configuration, never a hardcoded literal.
4. **Given** a production build, **when** it completes, **then** the initial bundle for the customer app
   is under 300KB compressed, enforced by a build budget that fails the build.

### Notes

The customer portal's bundle budget is a real constraint, not aspiration: people load it on a phone in a
queue. Admin has no equivalent budget.

### Out of scope

Containerisation of the portals; that is `STORY-0005-04-03`.

**OpenSpec change id:** `add-angular-workspace`

---

## STORY-0001-02-02 — Application shell and navigation

**As a** visitor
**I want** consistent header, navigation and footer on every page
**So that** I always know where I am and how to reach the shop.

### Acceptance criteria

1. **Given** any customer route, **when** it renders, **then** the shell shows the Brakeman's mark,
   primary navigation, and a footer carrying **the selected branch's** address, phone and today's hours.
   No address is hardcoded anywhere in the shell.
2. **Given** today is Sunday, **when** the footer renders, **then** it shows the shop as closed rather
   than showing a time range.
3. **Given** a viewport under 768px, **when** navigation renders, **then** it collapses to a menu that is
   operable by keyboard and closes on Escape.
4. **Given** the current route, **when** navigation renders, **then** the active item is marked both
   visually and with `aria-current`.

### Notes

Hours are Mon–Fri 6am–7pm, Sat 7am–7pm, Sun closed, with a documented seasonal override list. Model
hours as data from the start; hardcoding them means the seasonal list becomes a code change every December.

### Out of scope

The hours API. Hours are a static config file in this story and become an endpoint in EPIC-0006.

**OpenSpec change id:** `add-portal-shell-navigation`

---

## STORY-0001-02-03 — Loading, empty and error states

**As a** visitor on a slow connection
**I want** the app to tell me what is happening
**So that** I do not think it is broken.

### Acceptance criteria

1. **Given** a route whose data is loading, **when** it renders, **then** it shows a skeleton matching the
   shape of the content, not a spinner over a blank page.
2. **Given** a failed request, **when** the error state renders, **then** it says what failed and offers a
   retry that re-issues only the failed request.
3. **Given** a legitimately empty result, **when** it renders, **then** it is visually distinct from both
   the loading and the error state and suggests a next action.
4. **Given** an unhandled route, **when** it is reached, **then** a 404 page renders inside the shell with
   navigation intact.

### Out of scope

Offline support and service workers.

**OpenSpec change id:** `add-shell-state-components`

---

## STORY-0001-02-04 — Theme toggle

**As a** visitor
**I want** to override the system theme
**So that** I can read comfortably regardless of my device setting.

### Acceptance criteria

1. **Given** the shell, **when** I activate the theme control, **then** it cycles system → light → dark
   and the current mode is announced to assistive technology.
2. **Given** an explicit choice, **when** I return later, **then** it persists and applies before first
   paint with no flash of the wrong theme.
3. **Given** `prefers-reduced-motion`, **when** the theme changes, **then** no transition animates.

### Out of scope

Per-user server-side theme preference; this is device-local.

**OpenSpec change id:** `add-theme-toggle`
