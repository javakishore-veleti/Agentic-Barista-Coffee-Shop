# FEAT-0016-03 — Purchase, Reload & Balance Flows

**Epic:** EPIC-0016 · **Depends on:** FEAT-0016-02 · **Blocks:** nothing

## Description

The three conversations this agent exists to have.

---

## STORY-0016-03-01 — Purchase conversation

**As a** customer
**I want** to buy a gift card by chatting
**So that** it is faster than filling in a form.

### Acceptance criteria

1. **Given** a purchase intent, **when** the conversation runs, **then** the agent establishes amount,
   recipient, delivery channel, delivery timing and optional message, asking only for what is missing.
2. **Given** an amount outside configured bounds, **when** proposed, **then** the agent states the bound from
   the tool result rather than guessing it.
3. **Given** a scheduled delivery, **when** confirmed, **then** the agent states the delivery time **with its
   timezone** explicitly.
4. **Given** a completed purchase, **when** it succeeds, **then** the agent emits a `giftcard_preview` with the
   issued card's details from the tool result.
5. **Given** a payment failure, **when** it occurs, **then** the agent states plainly that no card was created
   and no money taken.

### Out of scope

Card artwork selection.

**OpenSpec change id:** `add-giftcard-purchase-conversation`

---

## STORY-0016-03-02 — Group card conversation

**As an** organiser
**I want** to set up a pooled card by chatting
**So that** collecting from colleagues is simple.

### Acceptance criteria

1. **Given** a group intent, **when** the conversation runs, **then** the agent establishes target amount,
   deadline, recipient and delivery, and returns the contribution link from the tool result.
2. **Given** contributions in progress, **when** the organiser asks, **then** the agent reports the pooled total
   from a fresh tool read.
3. **Given** the deadline having passed with no contributions, **when** asked, **then** the agent explains no
   card was issued and any authorisations were voided.

### Out of scope

Chasing contributors.

**OpenSpec change id:** `add-group-card-conversation`

---

## STORY-0016-03-03 — Balance and reload

**As a** customer
**I want** to check and top up my card
**So that** I can keep using it.

### Acceptance criteria

1. **Given** a balance request, **when** made, **then** the agent calls `check_balance` in that turn and states
   the figure with the timestamp it was computed at.
2. **Given** a card the principal has no rights to, **when** asked about, **then** the tool refuses and the
   agent explains without revealing whether the card exists.
3. **Given** a reload, **when** requested, **then** confirmation is required, the write is idempotent, and the
   agent reports the new balance from the tool result.
4. **Given** a registered customer, **when** they ask about their cards, **then** the agent reads the wallet
   rather than asking them to recite a card reference.

### Notes

Criterion 4 is the payoff of customer accounts and a brand-wide ledger meeting in one place — the customer
does not have to know their card number.

### Out of scope

Transferring a card to another person.

**OpenSpec change id:** `add-balance-and-reload-conversation`
