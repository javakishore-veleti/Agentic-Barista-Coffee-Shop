# FEAT-0011-04 — Delivery Scheduling & Group Cards

**Epic:** EPIC-0011 · **Depends on:** FEAT-0011-03 · **Blocks:** EPIC-0016

## Description

Getting the card to the recipient — now or later, by email or SMS — and the pooled group card.

---

## STORY-0011-04-01 — Delivery scheduling

**As a** customer buying a birthday gift
**I want** the card to arrive on the day
**So that** it is not early and awkward.

### Acceptance criteria

1. **Given** a purchase, **when** delivery is specified, **then** it accepts channel (email or SMS), recipient
   address or number, a delivery date and time, and an optional personal message.
2. **Given** a scheduled delivery, **when** stored, **then** the time is stored with an explicit timezone and
   returned with it — never as a bare local time.
3. **Given** "send now", **when** chosen, **then** delivery is attempted immediately and its outcome recorded.
4. **Given** a scheduled delivery, **when** its time arrives, **then** the delivery job sends exactly once,
   even if the job runs more than once.
5. **Given** a delivery failure, **when** it occurs, **then** it is recorded, retried per a documented policy,
   and surfaced to the purchaser — the card itself remains valid.

### Notes

Timezone explicitness matters across 1,000 US branches: "9am" without a zone is four different moments, and
the purchaser and recipient may not share one.

### Out of scope

Actual email and SMS provider integration; the mock records intent and outcome.

**OpenSpec change id:** `add-giftcard-delivery-scheduling`

---

## STORY-0011-04-02 — Group cards

**As a** group of colleagues
**I want** to pool money into one card
**So that** we can give a single larger gift.

### Acceptance criteria

1. **Given** a group card, **when** created, **then** it has an organiser, a target amount, a contribution
   deadline and a shareable contribution link.
2. **Given** a contribution, **when** made, **then** it is recorded with contributor and amount and is visible
   to the organiser.
3. **Given** the deadline, **when** reached, **then** the card is issued for the pooled total and delivered per
   the organiser's settings.
4. **Given** a group that fails to reach any contribution, **when** the deadline passes, **then** no card is
   issued and any authorisations are voided.
5. **Given** a contributor, **when** they view the group, **then** they see the total and their own
   contribution, and contributor amounts are hidden unless the organiser enables sharing.

### Out of scope

Refunding individual contributions after issuance.

**OpenSpec change id:** `add-group-gift-cards`

---

## STORY-0011-04-03 — Customer wallet

**As a** registered customer
**I want** my cards in one place
**So that** a global ledger is actually useful to me.

### Acceptance criteria

1. **Given** a customer principal, **when** the wallet is fetched, **then** it lists cards they purchased or
   received, with balances and last movement.
2. **Given** a card purchased as a guest, **when** that guest registers with the same email, **then** the card
   appears in the wallet.
3. **Given** a card delivered to an email that later registers, **when** registration completes, **then** the
   received card appears in that wallet.
4. **Given** a guest principal, **when** the wallet is requested, **then** it is refused with a code the portal
   renders as an invitation to register.

### Notes

This closes the loop on the branch model: the ledger is brand-wide, and the wallet is what makes that visible
to the person holding the card.

### Out of scope

Transferring a card between customers.

**OpenSpec change id:** `add-customer-giftcard-wallet`
