# FEAT-0003-01 — Chat Panel & SSE Client

**Epic:** EPIC-0003 · **Depends on:** FEAT-0001-03 · **Blocks:** FEAT-0003-03, FEAT-0003-04

## Description

One reusable chat component and one streaming client, parametrised by surface. Written once, used three times.

---

## STORY-0003-01-01 — The chat envelope

**As a** developer who will implement three agents in three frameworks
**I want** the request and event format specified and versioned before any agent exists
**So that** each framework implements a fixed target rather than inventing its own.

### Acceptance criteria

1. **Given** the specification, **when** reviewed, **then** it defines `POST /v1/chat/{surface}/{session_id}`
   returning `text/event-stream`, with `surface ∈ {barista, events, giftcards}`.
2. **Given** the event types, **when** enumerated, **then** exactly these exist: `token`, `tool_call`,
   `tool_result`, `ui_action`, `error`, `done`, each with a documented payload schema.
3. **Given** a `done` event, **when** it arrives, **then** it carries `tokens_in`, `tokens_out` and `cost_usd`.
4. **Given** an `error` event, **when** it arrives, **then** it carries a stable machine code and a `retryable`
   boolean, and the human-readable text is separate from the code.
5. **Given** the request body, **when** sent, **then** it carries `message` and a `context` object that always
   includes `branch_id`.
6. **Given** the schema, **when** it changes, **then** the version in the path or a header changes with it.

### Notes

`branch_id` is in the envelope, not in each surface's bespoke context, because all three domains are
branch-aware and none of them may guess. A request without one is a client bug and should be rejected.

### Out of scope

Any implementation. This story ships the specification and its schema files.

**OpenSpec change id:** `add-chat-envelope-spec`

---

## STORY-0003-01-02 — Streaming client

**As a** developer
**I want** one typed client that consumes the envelope
**So that** no component parses a stream by hand.

### Acceptance criteria

1. **Given** a stream, **when** events arrive, **then** the client emits typed, discriminated events and never
   surfaces a raw string to a component.
2. **Given** a dropped connection mid-stream, **when** it drops, **then** the client surfaces a retryable error
   and the partial assistant message is preserved and marked incomplete rather than discarded.
3. **Given** an in-flight stream, **when** the user sends again or navigates away, **then** the previous stream
   is cancelled and its resources released.
4. **Given** an unknown event type, **when** received, **then** it is ignored without breaking the stream,
   so an older client survives a newer agent.

### Out of scope

Reconnect-and-resume from a server-side offset.

**OpenSpec change id:** `add-chat-sse-client`

---

## STORY-0003-01-03 — Chat panel component

**As a** customer
**I want** a chat window that behaves like every other chat window I use
**So that** I do not have to learn it.

### Acceptance criteria

1. **Given** streaming tokens, **when** they arrive, **then** they append smoothly and the view auto-scrolls
   only while the user is already at the bottom.
2. **Given** a `tool_call` event, **when** received, **then** an unobtrusive activity indicator names what the
   assistant is doing in customer language ("checking the menu"), not the tool identifier.
3. **Given** the transcript, **when** navigated by keyboard, **then** each message is reachable, new messages
   are announced politely, and the input keeps focus after send.
4. **Given** a long transcript, **when** it grows, **then** rendering stays responsive and older messages are
   virtualised rather than all mounted.
5. **Given** a failed turn, **when** it fails, **then** the user's message remains editable and resendable.

### Out of scope

File upload, voice input, message editing after a successful response.

**OpenSpec change id:** `add-chat-panel-component`
