# Eigent Architecture Docs (Manual)

TL;DR: This directory is a manual, per-subsystem breakdown of Eigent (the
`eigent` submodule). Eigent is a desktop Electron app that spawns a local FastAPI
runtime and streams SSE events to the UI, with optional server persistence and
sharing. Paths below are relative to the `eigent/` submodule.

## Start here

- Repo map: `repo-map.md`
- Core runtime (Electron + backend + SSE): `subsystems/core-runtime.md`
- Tools and toolkits: `subsystems/tools.md`
- Model selection + prompting: `subsystems/models-prompting.md`
- Context window + compaction: `subsystems/context-compaction.md`
- Persistence + sharing: `subsystems/persistence-sharing.md`
- Safety + failure modes: `subsystems/safety-failure-modes.md`
- Onboarding checklist: `onboarding.md`

## System overview

```mermaid
flowchart LR
  subgraph Desktop
    UI[Renderer (React/Zustand) src/]
    EM[Electron main electron/main]
  end
  EM -->|spawn uvicorn| BE[AI Backend (FastAPI+CAMEL) backend/]
  UI -->|POST /chat SSE| BE
  BE -->|SSE steps| UI
  BE -->|sync_step POST /chat/steps| SV[Server (FastAPI+DB) server/]
  SV --> DB[(SQL Database)]
  EM -->|read/write| MCP[~/.eigent/mcp.json]
  BE --> TK[Toolkits backend/app/utils/toolkit/*]
  TK --> WB[Browser WS server HybridBrowserToolkit]
```
