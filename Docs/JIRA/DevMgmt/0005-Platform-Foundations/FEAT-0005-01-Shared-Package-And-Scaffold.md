# FEAT-0005-01 — Shared Package & Service Scaffold

**Epic:** EPIC-0005 · **Depends on:** nothing · **Blocks:** every other feature in Phase B

## Description

`Middleware/shared` (installable as `akiv-common`) plus the scaffold every FastAPI service starts from.
Its hardest constraint is what it must *not* contain.

---

## STORY-0005-01-01 — The shared package

**As a** developer starting any service
**I want** config, logging, tracing, errors and health checks available by import
**So that** six services do not each invent them.

### Acceptance criteria

1. **Given** the package, **when** imported, **then** it provides typed settings loading (pydantic-settings),
   structured JSON logging with trace correlation, an OpenTelemetry setup helper, a standard error envelope,
   and `/healthz` plus `/readyz` handlers.
2. **Given** the dependency list, **when** inspected, **then** it contains **no** agent framework, no
   `google-adk`, no `langchain`, and no database driver — only what all six services genuinely share.
3. **Given** a service importing it, **when** the service also installs ADK or LangChain, **then** no version
   conflict arises from the shared package.
4. **Given** the error envelope, **when** any service returns an error, **then** it carries a stable machine
   code, a human message, and the trace id.

### Notes

The dependency constraint is the whole point. ADK pins `google-genai` and `langchain-google-genai` pins it
differently; if the shared package pulls either in, the three agent services can no longer coexist in one
repo. Enforce it with a test that asserts the resolved dependency set.

### Out of scope

Domain models of any kind.

**OpenSpec change id:** `add-shared-package`

---

## STORY-0005-01-02 — Service scaffold

**As a** developer
**I want** a documented starting point for a new FastAPI service
**So that** services are consistent without a code generator to maintain.

### Acceptance criteria

1. **Given** the scaffold, **when** followed, **then** a new service has settings, logging, tracing, health
   endpoints, an OpenAPI document, a Dockerfile and a test harness in under an hour.
2. **Given** any service, **when** it starts, **then** it logs its resolved configuration — provider, store,
   auth provider — at info level, with secrets redacted.
3. **Given** `/readyz`, **when** a required dependency is unavailable, **then** it reports not-ready rather
   than ready-with-degraded-behaviour.
4. **Given** the Dockerfile, **when** built, **then** it produces a non-root image with a pinned base and a
   documented build argument for the service name.

### Notes

"Logs its resolved configuration at startup" is small and pays for itself daily once three providers and
three store modes are in play.

### Out of scope

A cookiecutter or generator tool. A documented reference service is enough for six services.

**OpenSpec change id:** `add-service-scaffold`

---

## STORY-0005-01-03 — Migration and seed conventions

**As a** developer
**I want** one migration approach across every service that owns tables
**So that** `docker-all-up.sh` can migrate everything the same way.

### Acceptance criteria

1. **Given** any service with a database, **when** migrations run, **then** they run through Alembic from that
   service's own directory against its own schema or database.
2. **Given** a fresh environment, **when** migrations and seeds run, **then** the result is identical to
   running them against an existing environment already at that revision.
3. **Given** seed data, **when** applied, **then** it is idempotent and clearly separated from migrations.
4. **Given** a migration, **when** it is written, **then** it is reversible or explicitly documents why not.

### Out of scope

Zero-downtime migration strategy.

**OpenSpec change id:** `add-migration-conventions`
