# FEAT-0017-03 — Provider Conformance Suite

**Epic:** EPIC-0017 · **Depends on:** EPIC-0013 · **Blocks:** any non-default provider reaching a demo

## Description

**No provider is "supported" until it passes this suite.** It is the gate, not a report.

---

## STORY-0017-03-01 — The conformance suite

**As a** team
**I want** one suite that measures tool-calling fidelity
**So that** "configurable provider" is a supported list rather than a hope.

### Acceptance criteria

1. **Given** the suite, **when** run against a provider, **then** it measures: correct tool selection from a
   realistic toolset, correct argument types, handling of a **required** parameter such as `branch_id`,
   multi-step tool use, parallel calls where supported, structured output adherence, and correct refusal when
   no tool fits.
2. **Given** each case, **when** run, **then** it repeats enough times to report a rate rather than a single
   pass or fail.
3. **Given** results, **when** produced, **then** they are written to a committed results file with the model,
   provider, date and rates.
4. **Given** a provider below the documented threshold on any required capability, **when** recorded, **then**
   it is marked unsupported and services refuse to start with it.

### Notes

Criterion 4 is what makes the suite load-bearing. A conformance report nobody enforces becomes a document
that describes a system nobody has.

### Out of scope

Quality of prose. This measures mechanics, not writing.

**OpenSpec change id:** `add-provider-conformance-suite`

---

## STORY-0017-03-02 — Framework path coverage

**As a** developer
**I want** both framework paths measured
**So that** ADK's LiteLLM route is not assumed to match LangChain's native one.

### Acceptance criteria

1. **Given** the suite, **when** run, **then** it executes against the ADK path and the LangChain path
   separately for every provider.
2. **Given** ADK with a non-Gemini provider, **when** measured, **then** the LiteLLM route is exercised
   explicitly and reported as its own row.
3. **Given** a divergence between the two paths for one provider, **when** found, **then** it is reported
   rather than averaged away.

### Notes

This is the specific risk the plan flags. The same model can be reliable through one framework and unreliable
through another, and averaging the two hides exactly the failure that would bite.

### Out of scope

Fixing framework bugs; the outcome is a supported matrix.

**OpenSpec change id:** `add-framework-path-conformance`

---

## STORY-0017-03-03 — Self-hosted model coverage

**As a** team on a $0 budget
**I want** the default model measured first
**So that** the critical path is validated before agent work.

### Acceptance criteria

1. **Given** the suite, **when** run, **then** the configured self-hosted default is included as a first-class
   row, not an afterthought.
2. **Given** several candidate local models, **when** measured, **then** results are comparable so a smaller or
   larger model can be chosen on evidence.
3. **Given** a local model failing the threshold, **when** recorded, **then** the documented fallback — a
   free-tier hosted model for that surface only — is exercised and shown to pass.
4. **Given** the suite, **when** run on a developer machine, **then** it completes in a documented time so it
   is actually run.

### Out of scope

Model fine-tuning.

**OpenSpec change id:** `add-selfhosted-model-conformance`
