# FEAT-0004-04 — Event Inquiries & Gift Card Ledger

**Epic:** EPIC-0004 · **Depends on:** FEAT-0004-01 · **Blocks:** EPIC-0010, EPIC-0011, EPIC-0015

## Description

The two staff workflows that later become the human half of an agent conversation.

---

## STORY-0004-04-01 — Event inquiry queue

**As a** branch manager
**I want** a queue of event inquiries for my branches
**So that** nothing sits unanswered.

### Acceptance criteria

1. **Given** the queue, **when** it loads, **then** it shows inquiries for branches in my scope, sorted by
   event date, with status, guest count, event type and requested window.
2. **Given** an inquiry, **when** opened, **then** it shows every field the customer supplied — name, email,
   phone, event date and alternate date, event type, desired start and length, guest count, bar type — plus
   the quoted price and the full room window including setup and cleanup.
3. **Given** an inquiry, **when** I approve or decline it, **then** the decision is recorded with actor,
   timestamp and an optional note, and the customer-visible status changes.
4. **Given** a conflicting hold on the same room window, **when** the queue renders, **then** the conflict is
   flagged before I can approve both.

### Notes

This approval action is what later resolves the LangGraph interrupt in EPIC-0015. Designing the screen now
means the graph is built against a real decision point rather than a hypothetical one.

### Out of scope

Contracts, deposits and invoicing.

**OpenSpec change id:** `add-event-inquiry-queue`

---

## STORY-0004-04-02 — Gift card ledger views

**As** head office
**I want** to see issued cards and their movements
**So that** balances can be explained and disputes answered.

### Acceptance criteria

1. **Given** the ledger, **when** it loads, **then** it lists cards with current balance, issue date, issuing
   branch and status, searchable by card reference and recipient.
2. **Given** a card, **when** opened, **then** every movement — issue, load, redemption, adjustment — is shown
   in order with amount, timestamp, actor and the branch where it happened.
3. **Given** a card redeemed at a branch other than the issuing one, **when** viewed, **then** both branches
   are visible on the movement, because the ledger is brand-wide.
4. **Given** any balance shown, **when** rendered, **then** it is the sum of ledger movements, never a
   separately stored figure that could drift.

### Notes

Cross-branch redemption is the normal case, not an edge case. The screen should make inter-branch movement
obvious, since that is the reconciliation question finance will eventually ask.

### Out of scope

Adjustments and refunds as an action; this story is read-only.

**OpenSpec change id:** `add-giftcard-ledger-views`

---

## STORY-0004-04-03 — Gift card configuration

**As** head office
**I want** to configure denominations and which branches promote them
**So that** the purchase flow reflects a real commercial decision.

### Acceptance criteria

1. **Given** the configuration screen, **when** it loads, **then** standard denominations ($5, $10, $20, $50),
   custom-amount bounds, and whether group cards are enabled are editable by HQ.
2. **Given** branch participation, **when** edited, **then** which denominations a branch promotes and any
   branch-funded promotion are set per branch, within HQ-defined limits.
3. **Given** a configuration change, **when** saved, **then** it is effective-dated and does not alter cards
   already issued.

### Out of scope

Card artwork and physical card stock.

**OpenSpec change id:** `add-giftcard-configuration`
