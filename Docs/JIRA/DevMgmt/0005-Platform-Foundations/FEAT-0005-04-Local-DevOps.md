# FEAT-0005-04 — Local DevOps Scripts & Compose

**Epic:** EPIC-0005 · **Depends on:** FEAT-0005-01 · **Blocks:** every later demo

## Description

`DevOps/Local`: per-component compose files on one shared network, three scripts, and a status command that
answers the question you actually ask ten times a day.

---

## STORY-0005-04-01 — Compose topology

**As a** developer
**I want** each component's compose file separate but joinable
**So that** I can run infrastructure alone, or the whole stack, without editing YAML.

### Acceptance criteria

1. **Given** `DevOps/Local`, **when** inspected, **then** it contains `Postgres/`, `Redis/`, `Ollama/`,
   `Middleware/`, `Portals/` and `Observability/` compose files, plus `.env.shared`.
2. **Given** `network.sh`, **when** run repeatedly, **then** it creates the shared external network once and
   succeeds on every subsequent run.
3. **Given** any single component, **when** brought up alone, **then** it works without the others being
   present, failing readiness rather than crashing if it has unmet dependencies.
4. **Given** `.env.shared`, **when** absent, **then** the scripts stop with a message pointing at the example
   file rather than starting with defaults.

### Out of scope

Kubernetes manifests. Per the budget, serverless containers are the deployment target.

**OpenSpec change id:** `add-local-compose-topology`

---

## STORY-0005-04-02 — Up, down and ordering

**As a** developer
**I want** one command that brings the stack up in the right order
**So that** I am not restarting services because one started before its database.

### Acceptance criteria

1. **Given** `docker-all-up.sh`, **when** run, **then** it executes in order: network, Postgres and Redis with
   a **wait for healthcheck**, migrations, index bootstrap if the corpus is newer than the index, domain APIs
   and MCP servers with a wait for `/healthz`, agent services, gateway, portals.
2. **Given** any step failing, **when** it fails, **then** the script stops, reports which step and why, and
   does not proceed to dependent steps.
3. **Given** `docker-all-down.sh`, **when** run, **then** it stops components in reverse order and takes
   `--volumes` to also drop data.
4. **Given** the scripts, **when** run twice in a row, **then** the second run is a no-op or a clean restart,
   never a partial failure from existing state.

### Notes

The ordering and health-gating *is* the value of these scripts. A script that just wraps `docker compose up`
adds nothing over typing it.

### Out of scope

Hot reload and watch mode.

**OpenSpec change id:** `add-docker-all-scripts`

---

## STORY-0005-04-03 — Status with resolved configuration

**As a** developer
**I want** status to tell me which model and store each service is actually using
**So that** I stop guessing why an answer looks wrong.

### Acceptance criteria

1. **Given** `docker-all-status.sh`, **when** run, **then** it prints per service: container state, `/healthz`
   result, and the **resolved** LLM provider and model, embedding triple, store mode and auth provider.
2. **Given** a service that is down, **when** status runs, **then** it is reported as down without the script
   hanging or failing.
3. **Given** the output, **when** read, **then** it fits legibly in an 80-column terminal.

### Notes

With three provider options, three store modes and two auth realms per service, "which model is the events
agent using right now" is genuinely hard to answer from configuration files alone.

### Out of scope

A web dashboard.

**OpenSpec change id:** `add-docker-all-status`
