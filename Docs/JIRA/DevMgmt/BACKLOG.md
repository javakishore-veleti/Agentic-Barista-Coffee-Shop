# Backlog — Epic Index

Ordered. Each phase is demonstrable before the next begins.

## Phase A — Portals first (clarity before contracts)

| Epic | Name | Outcome | Features |
|------|------|---------|----------|
| [0001](0001-Design-System-And-Portal-Shell/EPIC.md) | Design System & Portal Shell | A branded, accessible Angular workspace both portals build on | 3 |
| [0002](0002-Storefront-And-Menu-Browse/EPIC.md) | Storefront & Menu Browse | Branch selection, the real menu, customisation, a priced cart | 4 |
| [0003](0003-Conversational-Surfaces/EPIC.md) | Conversational Surfaces | Three working chat windows against a contract stub | 4 |
| [0004](0004-Admin-Portal-Foundations/EPIC.md) | Admin Portal Foundations | Role-scoped staff screens for catalog, branches, inquiries, ledger | 4 |

## Phase B — Domain services

| Epic | Name | Outcome | Features |
|------|------|---------|----------|
| [0005](0005-Platform-Foundations/EPIC.md) | Platform Foundations | Shared package, the seams, local DevOps, gateway | 5 |
| [0006](0006-Identity-And-Access/EPIC.md) | Identity & Access | `authz-api`: one principal shape from four providers | 4 |
| [0007](0007-Branch-And-Tenancy-Model/EPIC.md) | Branch & Tenancy Model | 1,000 branches, tiers, availability, scope enforcement | 4 |
| [0008](0008-Catalog-Service/EPIC.md) | Catalog Service | Brand-wide menu, branch availability, deterministic pricing | 4 |
| [0009](0009-Orders-And-Commerce/EPIC.md) | Orders & Commerce | Header/lines/discounts/taxes/shipping, mocked POS | 4 |
| [0010](0010-Events-Service/EPIC.md) | Events Service | Branch-tiered rate cards, slot math, holds, approval | 4 |
| [0011](0011-GiftCards-Service/EPIC.md) | Gift Cards Service | Brand-wide ledger behind Square-shaped mocks | 4 |
| [0012](0012-Corpus-And-Indexing/EPIC.md) | Corpus & Indexing | 300k shards generated, deduped, indexed, embedded once | 4 |

## Phase C — Tools and agents, running locally

| Epic | Name | Outcome | Features |
|------|------|---------|----------|
| [0013](0013-MCP-Tool-Layer/EPIC.md) | MCP Tool Layer | One tool definition per capability, read/write split | 4 |
| [0014](0014-Barista-Agent-ADK/EPIC.md) | Barista Agent (Google ADK) | Grounded, branch-aware coffee ordering | 4 |
| [0015](0015-Events-Agent-LangGraph/EPIC.md) | Events Agent (LangGraph) | Checkpointed booking with human approval | 4 |
| [0016](0016-GiftCards-Agent-LangChain/EPIC.md) | Gift Cards Agent (LangChain) | Purchase, reload and balance by conversation | 3 |
| [0017](0017-Observability-And-Evals/EPIC.md) | Observability & Evals | Traces, cost, provider conformance, behavioural evals | 4 |

## Phase D — Delivery

| Epic | Name | Outcome | Features |
|------|------|---------|----------|
| [0018](0018-Cloud-Provisioning-Workflows/EPIC.md) | Cloud Provisioning Workflows | Numbered manual Setup/Destroy — CloudFormation on AWS, Terraform on Azure and GCP | 5 |

## Cross-epic dependency notes

- **0003 depends on 0001 only.** It ships against `FEAT-0003-02`'s mock agent service, not the real agents.
  That is the entire point of building the UI first — do not reorder it.
- **0006 and 0007 are load-bearing for everything after them.** Branch scope is enforced in the domain APIs
  using the principal from `authz-api`; an API built before those two will have authorization retrofitted,
  which is how scope bugs ship.
- **0013 (MCP) sits between the APIs and the agents on purpose.** Without it, `search_menu` is written three
  times — once per framework — and the three definitions drift.
- **0008 before 0014**, **0010 before 0015**, **0011 before 0016**. An agent is never built against an API
  that does not exist; its tools are HTTP calls behind MCP.
- **0012 blocks the `json` and `hybrid` store modes**, not `postgres`. Every service can ship on Postgres
  first and gain the other modes when the corpus lands.
- **0017's provider conformance suite gates any non-Gemini provider** reaching a demo — including Ollama,
  which is in the matrix from the start rather than reached for in a crisis.
- **0018 depends on everything having a container image**, delivered incrementally from 0005 onward.

## Three decisions that shape most of the backlog

1. **The menu is brand-wide; events are branch-specific; the gift card ledger is global.** This is why 0007
   exists as its own epic and why `branch_id` is a required parameter almost everywhere.
2. **Reference data is pluggable; transactional data is Postgres, always.** Orders, bookings, holds and the
   ledger are never file-backed, whatever `<DOMAIN>_STORE` says.
3. **Nothing a model says about money or availability is trusted.** Every figure comes from a tool call in
   the current turn. This shows up as acceptance criteria in every agent epic, not as a prompt instruction.
