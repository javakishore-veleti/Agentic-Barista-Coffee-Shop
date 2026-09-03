# FEAT-0005-05 — Gateway (BFF)

**Epic:** EPIC-0005 · **Depends on:** FEAT-0005-01 · **Blocks:** EPIC-0014, 0015, 0016

## Description

The single entry point the portals talk to: token verification, session identity, trace propagation, rate
limiting, and SSE fan-out to the three agent services.

---

## STORY-0005-05-01 — Routing and SSE pass-through

**As a** portal
**I want** one origin to call
**So that** the front end has no knowledge of internal service topology.

### Acceptance criteria

1. **Given** `POST /v1/chat/{surface}/{session_id}`, **when** called, **then** the gateway routes to the agent
   service for that surface and streams the response through unmodified.
2. **Given** a streaming response, **when** it is proxied, **then** events are forwarded without buffering, so
   first-token latency is not degraded.
3. **Given** a client disconnect, **when** it happens, **then** the upstream request is cancelled rather than
   left running.
4. **Given** an unknown surface, **when** requested, **then** a 404 with a stable error code is returned.

### Out of scope

A supervisor that routes one chat box across three agents.

**OpenSpec change id:** `add-gateway-routing`

---

## STORY-0005-05-02 — Token verification and internal context

**As a** domain service
**I want** the gateway to verify identity once and hand me a trusted context
**So that** six services do not each call the identity provider.

### Acceptance criteria

1. **Given** an inbound request, **when** it carries a token, **then** the gateway verifies it and forwards a
   **signed** internal context header carrying the principal.
2. **Given** a request with no token, **when** it targets a public endpoint, **then** an anonymous guest
   principal is forwarded rather than the request being rejected.
3. **Given** an internal context header on a request that did not come through the gateway, **when** a service
   receives it, **then** the signature check rejects it.
4. **Given** any request, **when** it enters, **then** a trace id is created or continued and propagated to
   every downstream call.

### Notes

Criterion 3 matters: without a signature the internal context header is a trivial privilege escalation for
anything that can reach a service directly.

### Out of scope

Token issuance; that is `authz-api` in EPIC-0006.

**OpenSpec change id:** `add-gateway-auth-context`

---

## STORY-0005-05-03 — Rate limiting and request bounds

**As an** operator
**I want** basic protection at the edge
**So that** one client cannot exhaust a self-hosted model.

### Acceptance criteria

1. **Given** a principal or client address, **when** it exceeds a configured rate, **then** further requests
   receive a 429 with a `Retry-After` header and a stable error code.
2. **Given** the chat endpoint, **when** a request body exceeds the configured maximum, **then** it is
   rejected before reaching an agent.
3. **Given** limits, **when** configured, **then** they differ per surface, because a chat turn and a catalog
   read are not comparable in cost.
4. **Given** a rate-limited response, **when** the portal receives it, **then** it renders a clear message
   rather than a generic failure.

### Notes

This matters more than usual under the $0 posture — a single self-hosted model serving every surface is a
genuine shared resource, and one runaway client degrades the whole demo.

### Out of scope

Distributed rate limiting across replicas.

**OpenSpec change id:** `add-gateway-rate-limiting`
