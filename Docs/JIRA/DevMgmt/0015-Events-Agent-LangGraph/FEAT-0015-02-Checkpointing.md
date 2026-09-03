# FEAT-0015-02 — Postgres Checkpointing & Resume

**Epic:** EPIC-0015 · **Depends on:** FEAT-0015-01 · **Blocks:** FEAT-0015-03

## Description

The state that outlives the browser tab. Without this, the interrupt in the next feature is impossible.

---

## STORY-0015-02-01 — Checkpointer

**As a** customer
**I want** my half-finished booking to survive
**So that** closing the tab does not cost me the conversation.

### Acceptance criteria

1. **Given** the graph, **when** it runs, **then** state is checkpointed to Postgres after every node.
2. **Given** the service being killed mid-conversation, **when** it restarts, **then** the conversation resumes
   from the last checkpoint with no loss.
3. **Given** a checkpoint, **when** written, **then** it is keyed by a durable thread id derived from the
   session, not from an in-memory handle.
4. **Given** many concurrent conversations, **when** they run, **then** checkpoints do not interfere and each
   resumes independently.

### Notes

Postgres rather than Redis because this state must be durable, not merely fast. A booking half-agreed and then
lost is a worse failure than a slow one.

### Out of scope

Cross-region checkpoint replication.

**OpenSpec change id:** `add-langgraph-postgres-checkpointer`

---

## STORY-0015-02-02 — Resume semantics

**As a** customer returning days later
**I want** the conversation to pick up sensibly
**So that** I am not confused by stale context.

### Acceptance criteria

1. **Given** a resumed conversation, **when** it continues, **then** the agent summarises where things stood
   before asking anything new.
2. **Given** a resumed conversation whose quote has expired, **when** it resumes, **then** the agent says so
   and offers to re-quote.
3. **Given** a resumed conversation whose hold has lapsed, **when** it resumes, **then** availability is
   re-checked before anything is promised.
4. **Given** a conversation older than a documented maximum, **when** resumed, **then** it starts fresh with an
   explanation rather than resuming indefinitely stale state.

### Notes

This story is where the framework choice becomes visible to the customer. Resuming with a confidently stale
price would be worse than not resuming at all.

### Out of scope

Notifying the customer that their conversation is waiting.

**OpenSpec change id:** `add-booking-resume-semantics`

---

## STORY-0015-02-03 — Checkpoint retention

**As an** operator
**I want** checkpoints cleaned up
**So that** the table does not grow without bound.

### Acceptance criteria

1. **Given** a completed or abandoned conversation, **when** the retention job runs past its window, **then**
   its checkpoints are removed.
2. **Given** an active conversation, **when** the job runs, **then** it is untouched regardless of age.
3. **Given** removal, **when** it occurs, **then** the inquiry and booking records remain — only conversation
   state is discarded.

### Out of scope

Archiving conversation transcripts for analysis.

**OpenSpec change id:** `add-checkpoint-retention`
