# EPIC-0003 — Conversational Surfaces

**Phase:** A — Portals first
**Depends on:** EPIC-0001
**Blocks:** EPIC-0013 (the MCP layer), EPIC-0014–0016 (the agents)

## Intent

Three chat windows — barista, events, gift cards — fully built, streaming, and rendering rich responses,
**before a single agent exists**. They run against a mock agent service that speaks the real SSE envelope.

## Why this is not backwards

The chat wire format is the contract between the front end and every agent we will later write in three
different frameworks. Designing it against a stub we control means the envelope is settled by what the UI
genuinely needs, and each agent then implements a fixed target instead of negotiating one.

It also means epic 0003 has no dependency on epics 0014–0016. **Do not "simplify" this by waiting for the
real agents** — that inverts the dependency and puts the wire format in the hands of whichever framework
happens to be written first.

## Success measures

- All three windows stream, render tool activity, and display rich cards, driven only by the mock.
- Swapping the mock for a real agent changes one environment variable and no component code.
- The envelope has not changed by the time epic 0016 ships. If it has, this epic under-specified it.

## Features

| ID | Name | Stories |
|----|------|---------|
| [FEAT-0003-01](FEAT-0003-01-Chat-Panel-And-SSE-Client.md) | Chat Panel & SSE Client | 3 |
| [FEAT-0003-02](FEAT-0003-02-Mock-Agent-Service.md) | Mock Agent Service | 3 |
| [FEAT-0003-03](FEAT-0003-03-UI-Action-Renderers.md) | Rich `ui_action` Renderers | 3 |
| [FEAT-0003-04](FEAT-0003-04-Three-Surfaces-Wired.md) | Three Surfaces Wired | 3 |

## Out of scope

Any real model call, any real tool, any agent framework.
