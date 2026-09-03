# EPIC-0005 — Platform Foundations

**Phase:** B · **Depends on:** nothing (may run alongside Phase A) · **Blocks:** all of Phase B

## Intent

The shared Python package, the three configuration seams as protocols, the gateway, and a local environment
that comes up with one command. Nothing domain-specific — this epic ships the vocabulary the six services
are written in.

## Success measures

- A new FastAPI service can be scaffolded with config, logging, tracing and health checks in under an hour.
- `docker-all-up.sh` brings the whole stack up health-gated, and `docker-all-status.sh` reports each
  service's **resolved** provider and store.
- `Middleware/shared` has no heavy dependencies, so ADK and LangChain services can both import it.

## Features

| ID | Name | Stories |
|----|------|---------|
| FEAT-0005-01 | Shared Package & Service Scaffold | 3 |
| FEAT-0005-02 | LLM & Embedding Provider Abstraction | 3 |
| FEAT-0005-03 | Repository / Store Abstraction | 3 |
| FEAT-0005-04 | Local DevOps Scripts & Compose | 3 |
| FEAT-0005-05 | Gateway (BFF) | 3 |

## Out of scope

Any domain model. If it mentions coffee, events or gift cards, it belongs in a later epic.
