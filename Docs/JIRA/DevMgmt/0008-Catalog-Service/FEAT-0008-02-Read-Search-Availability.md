# FEAT-0008-02 — Read, Search & Availability API

**Epic:** EPIC-0008 · **Depends on:** FEAT-0008-01 · **Blocks:** EPIC-0013

## Description

The endpoints the portal and `catalog-mcp` consume. The defining rule: **semantic search runs once,
brand-wide; availability filtering runs per branch, relationally.**

---

## STORY-0008-02-01 — Menu read endpoints

**As a** portal or tool
**I want** to fetch the menu for a branch and date
**So that** I display only what is genuinely available.

### Acceptance criteria

1. **Given** a branch and date, **when** the menu is fetched, **then** it returns canonical items joined with
   that branch's availability, grouped by category.
2. **Given** an item unavailable at that branch that day, **when** the menu is fetched, **then** it is excluded
   by default and included only when explicitly requested, flagged unavailable.
3. **Given** an item with a rotation, **when** returned, **then** today's actual selection is included.
4. **Given** no branch id, **when** the endpoint is called, **then** it returns 400 per the required-branch
   rule.
5. **Given** a resolved price, **when** returned, **then** it reflects any effective override and states its
   source.

### Out of scope

Personalisation and recommendation ranking.

**OpenSpec change id:** `add-menu-read-endpoints`

---

## STORY-0008-02-02 — Semantic and lexical search

**As a** customer asking for "something iced and not too sweet"
**I want** the catalog searched by meaning
**So that** I find items whose description matches even when the words do not.

### Acceptance criteria

1. **Given** a query and a branch id, **when** search runs, **then** it embeds the query, searches the
   **brand-wide canonical items**, then filters results by that branch's availability for the date.
2. **Given** the search index, **when** inspected, **then** it contains one vector per **distinct** item — not
   one per branch, and not one per shard.
3. **Given** a query that is better served lexically — an exact item name — **when** run, **then** lexical and
   semantic results are combined with a documented ranking.
4. **Given** filters for tags, allergens or category, **when** supplied, **then** they are applied
   relationally after retrieval, not encoded into the query text.
5. **Given** a query returning nothing available at that branch, **when** it happens, **then** the response
   distinguishes "no match in the catalog" from "matched, but not available here today".

### Notes

Criterion 5 is what lets the barista agent say the honest thing: "we do make that, just not here today"
rather than "we do not have that", which would be false.

### Out of scope

Cross-branch suggestions such as "available at a branch two miles away".

**OpenSpec change id:** `add-catalog-search`

---

## STORY-0008-02-03 — Dietary filtering with substitution

**As a** customer who is lactose intolerant
**I want** filtering that understands a substitution
**So that** I am not refused a drink I could actually have.

### Acceptance criteria

1. **Given** a dairy-free filter, **when** applied, **then** results include items with no dairy allergen and
   items where a dairy-free milk is available at that branch, with the two cases distinguished.
2. **Given** an item returned because of a substitution, **when** returned, **then** the required modifier is
   named in the response.
3. **Given** a substitution that is unavailable at that branch today, **when** filtering, **then** the item is
   excluded, because the substitution is what made it eligible.

### Notes

This is the rule the agent's grounding depends on. Collapsing "dairy-free" and "can be made dairy-free"
produces an agent that either refuses valid orders or recommends unsafe ones.

### Out of scope

Cross-contamination warnings and allergen severity modelling.

**OpenSpec change id:** `add-dietary-filtering`

---

## STORY-0008-02-04 — Availability write endpoints

**As a** branch manager
**I want** to set availability and rotation through the API
**So that** the admin portal and any future tool use one path.

### Acceptance criteria

1. **Given** a principal with scope over the branch, **when** they set availability, **then** it is applied
   effective-dated and visible to reads immediately.
2. **Given** a principal without scope, **when** they attempt it, **then** it is refused with 403 and audited.
3. **Given** a rotation update, **when** applied, **then** the selection must come from that item's known
   options; an arbitrary string is refused.
4. **Given** a bulk availability change, **when** applied, **then** it is atomic across the affected rows.

### Out of scope

Editing canonical item definitions; that is HQ-only and a separate endpoint.

**OpenSpec change id:** `add-availability-write-endpoints`
