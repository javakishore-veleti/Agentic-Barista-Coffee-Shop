# FEAT-0015-03 — Human-in-the-Loop Interrupt

**Epic:** EPIC-0015 · **Depends on:** FEAT-0015-02, FEAT-0010-04 · **Blocks:** nothing

## Description

The slice that justifies the framework: the graph pauses, a human decides in the admin portal, and the
conversation continues — possibly days later.

**Build this early.** It is the clearest demonstration of why this domain got a graph, and leaving it to the
end means discovering its constraints too late.

---

## STORY-0015-03-01 — The interrupt

**As a** customer
**I want** to be told my booking is with staff
**So that** I know what is happening.

### Acceptance criteria

1. **Given** a submitted inquiry, **when** the graph reaches the approval node, **then** it interrupts,
   persists state, and tells the customer their request is with the branch.
2. **Given** an interrupted graph, **when** the customer sends another message, **then** the agent responds
   helpfully about status without advancing past the interrupt.
3. **Given** the interrupt, **when** it occurs, **then** the hold remains active for its TTL and the customer
   is told when it expires.
4. **Given** an interrupt whose hold expires before a decision, **when** that happens, **then** the graph
   routes to re-check availability rather than silently failing later.

### Out of scope

Escalation reminders to staff.

**OpenSpec change id:** `add-booking-approval-interrupt`

---

## STORY-0015-03-02 — Resolution from the admin portal

**As a** branch manager
**I want** my approval to continue the customer's conversation
**So that** they hear back without anyone re-typing anything.

### Acceptance criteria

1. **Given** a staff decision recorded via the approval API, **when** it emits its durable event, **then** the
   events agent consumes it and resumes the interrupted graph.
2. **Given** an approval, **when** resumed, **then** the graph converts the hold to a confirmed booking and the
   customer sees a confirmation with the agreed price and room window.
3. **Given** a decline, **when** resumed, **then** the graph releases the hold, conveys the customer-visible
   reason, and offers the alternate date.
4. **Given** the agent being down when the decision is made, **when** it comes back, **then** it picks up the
   decision from the durable event — nothing is lost.
5. **Given** a decision for a conversation whose checkpoint was retained, **when** resumed days later, **then**
   it still works.

### Notes

Criterion 4 is why the approval API emits a durable event rather than calling a webhook. The agent must be
allowed to be down.

### Out of scope

Staff replying with free text into the customer's conversation.

**OpenSpec change id:** `add-approval-interrupt-resolution`

---

## STORY-0015-03-03 — End-to-end demonstration

**As a** stakeholder
**I want** to see the whole loop work
**So that** the architectural claim is demonstrated rather than described.

### Acceptance criteria

1. **Given** the running stack, **when** the scripted demonstration runs, **then** it: books through chat,
   reaches the interrupt, approves in the admin portal, and shows the customer conversation completing.
2. **Given** the demonstration, **when** run, **then** it includes restarting the agent service between the
   interrupt and the approval, proving durability.
3. **Given** the demonstration, **when** run, **then** it is automated and part of CI, not a manual script.

### Notes

Automating this is what stops the capability quietly regressing. It is the highest-value integration test in
the platform.

### Out of scope

Load testing the flow.

**OpenSpec change id:** `add-booking-e2e-demonstration`
