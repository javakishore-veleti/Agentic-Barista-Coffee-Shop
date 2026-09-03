# FEAT-0005-02 — LLM & Embedding Provider Abstraction

**Epic:** EPIC-0005 · **Depends on:** FEAT-0005-01 · **Blocks:** EPIC-0012, 0014, 0015, 0016

## Description

Two functions — `get_chat_model(service)` and `get_embedder(service)` — and the provider adapters behind
them. Chat and embedding providers are configured **independently**, because changing the embedder
invalidates every vector we own.

---

## STORY-0005-02-01 — Chat model factory

**As a** developer building any agent
**I want** the provider resolved from configuration
**So that** no agent code names a vendor.

### Acceptance criteria

1. **Given** `<SERVICE>_LLM_PROVIDER`, **when** set to `ollama`, `gemini`, `bedrock`, `azure_openai` or
   `huggingface`, **then** `get_chat_model` returns a working handle for that provider.
2. **Given** LangChain and LangGraph services, **when** they request a model, **then** they receive the native
   integration for that provider.
3. **Given** an ADK service with `gemini`, **when** it requests a model, **then** it receives the native ADK
   model; **given** any other provider, **then** it receives a LiteLLM-wrapped model.
4. **Given** an unknown or unset provider, **when** the service starts, **then** it fails at startup with a
   clear message naming the variable — never lazily at the first request.
5. **Given** any provider, **when** a call fails, **then** the error is normalised to the shared envelope so
   callers do not branch on vendor exception types.

### Notes

`ollama` is the default per the $1 budget, pointed at `OLLAMA_BASE_URL` so the same configuration works
against a laptop daemon or a self-hosted cloud instance.

### Out of scope

Retry, fallback chains and rate limiting — those belong with the agents that need them.

**OpenSpec change id:** `add-chat-model-factory`

---

## STORY-0005-02-02 — Embedding factory and dimension registry

**As a** developer indexing the catalog
**I want** embeddings resolved separately from chat, with dimensions tracked
**So that** two vector spaces can never be mixed in one index.

### Acceptance criteria

1. **Given** `<SERVICE>_EMBED_PROVIDER` and `<SERVICE>_EMBED_MODEL`, **when** set, **then** `get_embedder`
   returns a handle independent of the chat provider setting.
2. **Given** an embedder, **when** queried, **then** it reports its `(provider, model, dim)` triple.
3. **Given** an index built with one triple, **when** a service starts configured with a different one,
   **then** it fails readiness with a message naming both triples — it does **not** silently write mixed
   vectors.
4. **Given** the registry, **when** inspected, **then** the known dimensions are recorded: 768 for
   `nomic-embed-text` and Gemini, 1024 for Titan v2, 1536 for Azure `text-embedding-3-small`, 384 for
   `bge-small`.

### Notes

Failing readiness on a triple mismatch is the single cheapest defence in this epic. The alternative is a
half-mixed index that returns plausible-looking wrong results and is only detectable by inspection.

### Out of scope

The re-embedding migration job; that is `STORY-0012-03-03`.

**OpenSpec change id:** `add-embedding-factory`

---

## STORY-0005-02-03 — Ollama in the local stack

**As a** developer
**I want** Ollama running in compose with the models pulled
**So that** the $0 default works on a fresh clone.

### Acceptance criteria

1. **Given** `docker-all-up.sh`, **when** it runs, **then** an Ollama service starts and the configured chat
   and embedding models are pulled if absent, with progress visible.
2. **Given** the model pull, **when** it has already happened, **then** it is skipped and the volume persists
   across `down` and `up`.
3. **Given** a machine that cannot run the configured model, **when** the stack starts, **then** the failure
   is reported clearly with the smaller model to try, rather than a timeout.
4. **Given** `OLLAMA_BASE_URL` pointing at an external host, **when** set, **then** the local Ollama container
   is skipped entirely.

### Out of scope

GPU scheduling and model quantisation choice.

**OpenSpec change id:** `add-ollama-local-stack`
