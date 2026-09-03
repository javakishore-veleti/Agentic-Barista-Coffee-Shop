# FEAT-0002-01 — Home & Shop Information

**Epic:** EPIC-0002 · **Depends on:** FEAT-0001-02 · **Blocks:** FEAT-0002-02

## Description

The page a first-time visitor lands on. Its job is to answer three questions fast: what is this place,
is it open, and how do I get there.

---

## STORY-0002-01-01 — Landing page

**As a** first-time visitor
**I want** to immediately understand what AKIV Coffee is and what I can do here
**So that** I can decide whether to order, book, or just get directions.

### Acceptance criteria

1. **Given** the landing route, **when** it loads, **then** the brand name, its positioning line, and three
   clear entry points (order, private events, gift cards) are visible without scrolling on a 360px viewport.
2. **Given** the page has loaded, **when** nothing has been interacted with, **then** all primary content is
   visible — no content waits on a scroll trigger to appear.
3. **Given** any entry point, **when** activated, **then** it navigates to the corresponding route and focus
   moves to that route's main heading.

### Out of scope

Marketing copy sign-off, photography, and any promotional or seasonal banner system.

**OpenSpec change id:** `add-landing-page`

---

## STORY-0002-01-02 — Hours, location and parking

**As a** customer planning a visit
**I want** to see today's hours, the address and where to park
**So that** I do not drive over to a closed shop.

### Acceptance criteria

1. **Given** the current date and time, **when** the hours block renders, **then** it states whether the shop
   is open right now and, if closed, when it next opens.
2. **Given** today is Sunday, **when** the hours block renders, **then** it shows closed rather than a range,
   and does not compute a "closes in N hours".
3. **Given** a date in the seasonal override list (Dec 24–25, Dec 26, Dec 31, Jan 1, Memorial Day, July 4th,
   Labor Day), **when** hours render, **then** the override wins over the regular weekday hours and is
   labelled as a holiday schedule.
4. **Given** the location block, **when** it renders, **then** it shows the **selected branch's** street
   address, a directions link, its phone number as a `tel:` link, and its parking guidance — all from branch
   data, never a literal in a template.

### Notes

Hours come from the branch fixture in this story, not an API. Model them as data with a weekly schedule plus
a dated override list, **per branch**, from the start — hardcoding either the schedule or the seasonal dates
makes every December a code change across 1,000 branches.

### Out of scope

Live "how busy is it" signals; an hours-editing screen (that is `FEAT-0004-02`).

**OpenSpec change id:** `add-hours-and-location`

---

## STORY-0002-01-03 — Menu fixture

**As a** developer building menu screens
**I want** a fixture file holding the real AKIV Coffee menu in the shape the catalog API will return
**So that** UI work proceeds before the API exists and the API inherits a shape proven by real screens.

### Acceptance criteria

1. **Given** the fixture, **when** it is inspected, **then** it contains every item on the current
   AKIV Coffee menu, including AKIV House Blend and Feature Roast drip, Latte, Lavender Latte, Chai, Cold Brew,
   Nitro, Mocha, Cappuccino, Hot Chocolate, Honeycomb, Coconut Mocha, Café Au Lait, Americano, Matcha Latte,
   Earl Grey Fog, the tea list, Ice Box shakes and smoothies, Strawberry Honey Matcha, rotating pastries,
   Butter Bars, local kombucha, and retail (12oz whole bean $17.50, mugs $12–$30, shirt $24).
2. **Given** an item available hot or iced, **when** it is represented, **then** temperature is a modifier
   with its own availability, not two separate items.
3. **Given** an item with a rotating selection (pastries, kombucha, tea), **when** it is represented, **then**
   it carries a `selection_at_counter` flag and the known options list, because the customer chooses on arrival.
4. **Given** every item, **when** validated against the fixture schema, **then** it declares tags, allergens,
   available milks, and whether it is a drink, bakery, or retail item.
5. **Given** the fixture, **when** the schema changes later, **then** a validation script fails loudly rather
   than screens silently rendering blanks.

### Notes

`selection_at_counter` matters more than it looks: the barista agent must not invent today's muffin.
It can say a muffin is available and that the choice happens at the case, which is what the shop actually does.

### Out of scope

Generating the 100k-shard corpus; that is EPIC-0010. This is one honest file.

**OpenSpec change id:** `add-menu-fixture`


---

## STORY-0002-01-04 — Branch selection

**As a** customer of a chain with 1,000 branches
**I want** the site to know which AKIV Coffee I mean
**So that** I see what is actually available where I am going.

### Acceptance criteria

1. **Given** no branch has been chosen, **when** any menu, order or events route is reached, **then** a branch
   picker is presented before menu content, and no branch is silently assumed.
2. **Given** the picker, **when** it opens, **then** branches are searchable by city, state and postcode, and
   the nearest branches are offered first if the customer grants location access.
3. **Given** a chosen branch, **when** I navigate or reload, **then** the choice persists and is visible in the
   shell at all times, with a one-tap way to change it.
4. **Given** a chosen branch, **when** it is displayed, **then** its own hours, address and parking notes are
   shown — not the the reference branch defaults.
5. **Given** a branch change with a non-empty cart, **when** I confirm the change, **then** lines unavailable at
   the new branch are flagged before the switch completes rather than silently dropped.

### Notes

Defaulting to a flagship branch is the failure mode to design against. The whole reason `branch_id` is a
required parameter on the MCP tools is that a confidently wrong answer about availability is worse than a
question — the UI must hold the same line.

Hours, address and parking are per branch from this story onward — there is no default branch and no
fallback address. The fixture's reference branch (Mon–Fri 6am–7pm, Sat 7am–7pm, Sun closed) is seed data, not the
platform's identity.

### Out of scope

Branch-level SEO landing pages; the branch admin screens (`FEAT-0004-03`).

**OpenSpec change id:** `add-branch-selection`
