# FEAT-0002-02 — Menu Browse & Filter

**Epic:** EPIC-0002 · **Depends on:** FEAT-0002-01, FEAT-0001-03 · **Blocks:** FEAT-0002-03

## Description

The menu as a browsable, filterable grid. This screen is the reference implementation of "grounded in the
catalog" — whatever it can show, the agent may recommend; whatever it cannot, the agent may not.

---

## STORY-0002-02-01 — Menu grid

**As a** customer
**I want** to see the whole menu grouped sensibly
**So that** I can find what I want without reading a wall of text.

### Acceptance criteria

1. **Given** the menu route, **when** it loads, **then** items are grouped into Espresso & Coffee, Tea & Matcha,
   Ice Box, Bakery, Kombucha and Retail, with group headings that are real landmarks for assistive technology.
2. **Given** each item card, **when** it renders, **then** it shows name, short description, price or price
   range, and dietary badges, with identical card edges and padding across the row.
3. **Given** an item priced by size, **when** its card renders, **then** it shows the lowest price prefixed
   appropriately rather than a single misleading figure.
4. **Given** any viewport from 360px to 1440px, **when** the grid renders, **then** the column count fills
   without a single orphaned card stretched across dead space.

### Out of scope

Search. Filtering is `STORY-0002-02-02`; free-text search arrives with the API in `FEAT-0006-02`.

**OpenSpec change id:** `add-menu-grid`

---

## STORY-0002-02-02 — Dietary and preference filters

**As a** customer who is lactose intolerant
**I want** to filter the menu to what I can actually drink
**So that** I am not reading allergen lists on nineteen items.

### Acceptance criteria

1. **Given** the filter controls, **when** I select "dairy-free", **then** only items with no dairy allergen
   *or* with a dairy-free milk available are shown, and items shown only because a substitution is possible
   are marked as such.
2. **Given** multiple filters across different groups, **when** applied, **then** they combine as AND across
   groups and OR within a group.
3. **Given** active filters, **when** the result set is empty, **then** an empty state names which filter
   combination excluded everything and offers to clear the narrowest one.
4. **Given** active filters, **when** I reload or share the URL, **then** the filter state is in the query
   string and restores exactly.
5. **Given** any filter change, **when** it applies, **then** the result count is announced to assistive
   technology.

### Notes

The substitution distinction is the whole story. A Latte is not dairy-free, but a Latte with oat milk is —
and Honeycomb is specified with oat milk by default. Collapsing those cases produces an agent that either
refuses valid orders or recommends unsafe ones.

### Out of scope

Persisting dietary preferences to a customer profile; there are no accounts yet.

**OpenSpec change id:** `add-dietary-filters`

---

## STORY-0002-02-03 — Catalog client with a swappable transport

**As a** developer
**I want** all menu reads to go through one typed client
**So that** swapping the fixture for the real API in EPIC-0006 is a one-line change.

### Acceptance criteria

1. **Given** any component needing menu data, **when** it fetches, **then** it calls the catalog client and
   never `HttpClient` directly.
2. **Given** an environment flag, **when** set to `fixture`, **then** the client resolves from the local
   fixture; when set to `http`, it calls `catalog-api`, with no other code changing.
3. **Given** the client, **when** the response fails schema validation, **then** it throws a typed error
   naming the offending field rather than propagating a malformed object into a template.

### Out of scope

Caching and request de-duplication.

**OpenSpec change id:** `add-catalog-client`
