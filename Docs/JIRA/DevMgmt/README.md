# AKIV Barista Coffee Shop Agentic Platform — Development Management

Product backlog for the AKIV Coffee agentic platform, written product-owner-first
and shaped so that each **story** is a self-contained input to OpenSpec.

## How this folder is organised

```
Docs/JIRA/DevMgmt/
  README.md                     <- you are here: conventions and the OpenSpec pipeline
  BACKLOG.md                    <- the ordered epic index; the single source of sequence
  000N-<Epic-Name>/
    EPIC.md                     <- epic intent, success measures, feature index
    FEAT-000N-0M-<Name>.md      <- one feature, containing its stories
```

## Identifier scheme

| Level   | Format                | Example              |
|---------|-----------------------|----------------------|
| Epic    | `EPIC-000N`           | `EPIC-0006`          |
| Feature | `FEAT-000N-0M`        | `FEAT-0006-02`       |
| Story   | `STORY-000N-0M-0K`    | `STORY-0006-02-03`   |

Identifiers are permanent. If a story is dropped, mark it `Withdrawn` in place rather than
renumbering — OpenSpec change folders and git history reference these ids.

## Story format

Every story carries the same five parts, and OpenSpec consumes all five:

1. **Narrative** — As a / I want / So that.
2. **Acceptance criteria** — Given / When / Then, numbered, independently testable.
3. **Notes** — constraints, edge cases, and the decisions already made so the agent does not re-litigate them.
4. **Out of scope** — what this story explicitly does not do. This is the most valuable line in the file;
   it is what stops a coding agent from widening the change.
5. **OpenSpec change id** — the kebab-case name to pass to `/opsx:propose`.

## The OpenSpec pipeline

This backlog stops at the *what*. OpenSpec owns the *how*.

```
PO writes STORY-0006-02-03 here
   |
   |  /opsx:explore          (optional, for stories marked "needs exploration")
   v
/opsx:propose add-menu-semantic-search
   -> openspec/changes/add-menu-semantic-search/
        proposal.md   why + what changes
        specs/        requirements and scenarios, derived from the AC above
        design.md     technical approach
        tasks.md      the implementation checklist  <- OpenSpec derives these, not us
   |
   v
/opsx:apply     implement the tasks
/opsx:verify    check implementation matches spec
/opsx:archive   fold specs into the baseline
```

**One story, one OpenSpec change.** Do not batch a feature into a single change — the acceptance
criteria are written at story granularity precisely so each `/opsx:apply` stays inside a reviewable diff.

Stories flagged **`needs exploration`** should get `/opsx:explore` first: those are the ones where the
approach is genuinely open and a proposal written cold would guess.

## Build sequence

The epic order in `BACKLOG.md` is deliberate and is the answer to "what do we build next":

1. **UI first (0001–0004)** — the portals are built against contract stubs, so the API shape is settled
   by looking at real screens rather than by argument. This is why `FEAT-0003-02 Mock Agent Service`
   exists and why it ships before any agent does.
2. **FastAPI services (0005–0010)** — the contracts the UI already consumes, made real.
3. **MCP tool layer (0011)** — every agent capability defined once as an MCP server, so the same
   `search_menu` is not written three times in three framework dialects.
4. **Agents, locally (0012–0015)** — ADK, LangGraph and LangChain, consuming those MCP tools.
5. **Cloud workflows (0016)** — manually-triggered provisioning once there is something to provision.

## Definition of Ready

A story may enter `/opsx:propose` when it has a narrative, at least two acceptance criteria, a named
out-of-scope line, and no unresolved dependency in `Depends on`.

## Definition of Done

Acceptance criteria demonstrably pass; `/opsx:verify` reports the implementation matches the spec;
the change is archived; and for anything user-facing, it is reachable in the running app via
`DevOps/Local/docker-all-up.sh` with no manual setup steps beyond the documented `.env`.
