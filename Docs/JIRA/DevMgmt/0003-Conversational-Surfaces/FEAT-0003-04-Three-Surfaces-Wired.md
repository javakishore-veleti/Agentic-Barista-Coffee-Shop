# FEAT-0003-04 — Three Surfaces Wired

**Epic:** EPIC-0003 · **Depends on:** FEAT-0003-01, FEAT-0003-02, FEAT-0003-03 · **Blocks:** nothing

## Description

The three windows in the customer portal, isolated from each other, each with its own session and its own
entry point.

---

## STORY-0003-04-01 — Three isolated surfaces

**As a** customer
**I want** the barista, events and gift card conversations kept separate
**So that** asking about a birthday party does not confuse my coffee order.

### Acceptance criteria

1. **Given** three surfaces, **when** each is opened, **then** each has its own session id namespaced by
   surface, and no transcript or context is shared between them.
2. **Given** a surface, **when** it is reopened after navigating away, **then** its own transcript is restored
   and the others are unaffected.
3. **Given** the selected branch, **when** any surface sends a message, **then** `branch_id` is included in the
   request context, and changing the branch is reflected in all three.
4. **Given** an unset branch, **when** a surface is opened, **then** the branch picker is presented before the
   chat is usable.

### Notes

Branch is the one piece of context deliberately shared across the three isolated surfaces. It is a property
of the customer's visit, not of a conversation.

### Out of scope

A supervisor or router that dispatches one chat box to three agents. The envelope leaves room for it; this
epic does not build it.

**OpenSpec change id:** `add-three-chat-surfaces`

---

## STORY-0003-04-02 — Entry points and deep links

**As a** customer
**I want** to reach the right conversation from where I already am
**So that** I do not start in the wrong window.

### Acceptance criteria

1. **Given** the menu screen, **when** I ask for help choosing, **then** I land in the barista surface with the
   item I was viewing already in context.
2. **Given** the private events page, **when** I start a conversation, **then** I land in the events surface.
3. **Given** a deep link to a surface with a seeded first message, **when** followed, **then** the surface opens
   and the seeded message is sent automatically exactly once.
4. **Given** a surface reached from elsewhere, **when** the conversation ends, **then** a clear path back to
   the originating screen exists.

### Out of scope

Marketing campaign parameters and attribution.

**OpenSpec change id:** `add-chat-entry-points`

---

## STORY-0003-04-03 — Transport switch

**As a** developer
**I want** each surface to point at either the mock or a real agent by configuration
**So that** epics 0014–0016 land without touching the front end.

### Acceptance criteria

1. **Given** an environment configuration, **when** a surface's transport is set to `mock` or `live`, **then**
   the surface uses that endpoint with no component code change.
2. **Given** mixed configuration, **when** one surface is `live` and two are `mock`, **then** all three work
   simultaneously.
3. **Given** a surface pointing at a live agent, **when** it streams, **then** the same components render the
   same way, because both speak the same envelope.

### Notes

This is the story that pays for the whole "UI first" ordering. It should be trivial by the time it is written;
if it is not, the envelope leaked into a component somewhere.

### Out of scope

Per-user or per-session transport selection.

**OpenSpec change id:** `add-chat-transport-switch`
