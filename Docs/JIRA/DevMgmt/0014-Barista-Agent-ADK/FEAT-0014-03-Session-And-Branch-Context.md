# FEAT-0014-03 — Session Memory & Branch Context

**Epic:** EPIC-0014 · **Depends on:** FEAT-0014-01 · **Blocks:** nothing

## Description

Conversation state, and the branch context that every tool call requires.

---

## STORY-0014-03-01 — Session memory

**As a** customer
**I want** the barista to remember what we just discussed
**So that** I am not repeating myself.

### Acceptance criteria

1. **Given** a session, **when** it continues, **then** prior turns are available to the agent within a
   documented window.
2. **Given** sessions, **when** stored, **then** they are namespaced by surface so the barista shares nothing
   with the events or gift card conversations.
3. **Given** a session, **when** the service restarts, **then** the conversation continues.
4. **Given** a session exceeding the window, **when** it grows, **then** older turns are summarised or dropped
   by a documented rule rather than silently truncated mid-message.

### Out of scope

Long-term memory across sessions; that arrives with customer accounts if wanted.

**OpenSpec change id:** `add-barista-session-memory`

---

## STORY-0014-03-02 — Branch context

**As a** customer
**I want** the barista to know which branch I am ordering from
**So that** everything it says applies to me.

### Acceptance criteria

1. **Given** a conversation, **when** it starts, **then** `branch_id` comes from the request context and is
   used on every branch-scoped tool call.
2. **Given** no branch in context, **when** a message arrives, **then** the agent asks which branch rather than
   calling tools — and the tools would refuse anyway.
3. **Given** a branch change mid-conversation, **when** it occurs, **then** the agent acknowledges it and
   re-checks availability for anything already discussed.
4. **Given** a cart built at one branch, **when** the branch changes, **then** unavailable lines are flagged
   before proceeding.

### Notes

Criterion 2 has belt and braces: the agent asks, and the tool schema makes `branch_id` required so a confused
agent cannot succeed anyway.

### Out of scope

Inferring a branch from location; the portal offers a choice.

**OpenSpec change id:** `add-barista-branch-context`

---

## STORY-0014-03-03 — Cart continuity

**As a** customer
**I want** the cart the barista builds to be the cart I see
**So that** switching between chatting and clicking is seamless.

### Acceptance criteria

1. **Given** the agent adding an item, **when** it does, **then** it mutates the same cart the portal renders,
   through the orders write tools.
2. **Given** a customer editing the cart in the portal, **when** they do, **then** the agent sees the current
   cart on its next tool call.
3. **Given** a guest, **when** they chat, **then** the cart is bound to the guest identifier and survives
   migration on registration.

### Out of scope

Real-time push of portal cart changes into an idle conversation.

**OpenSpec change id:** `add-barista-cart-continuity`
