# OpenCode Architecture Docs (Manual)

TL;DR: This directory is a manual, per-subsystem breakdown of the OpenCode
codebase (the `opencode` submodule). It focuses on the client/server runtime,
sessions and message parts, tool routing, model prompting, context compaction,
and sharing/sync. Paths below are relative to the `opencode/` submodule.

## Start here

- Repo map: `repo-map.md`
- Core runtime (sessions, messages, processor): `subsystems/core-runtime.md`
- Tools and tool state: `subsystems/tools.md`
- Model selection + prompting: `subsystems/models-prompting.md`
- Context window + compaction: `subsystems/context-compaction.md`
- Persistence + sharing: `subsystems/persistence-sharing.md`
- Onboarding checklist + mental model: `onboarding.md`

## System overview

```mermaid
flowchart LR
  subgraph Clients
    CLI[TUI CLI]
    SDK[SDK Client]
    Desktop[Desktop App]
    Web[Web/Console]
  end

  subgraph Core[OpenCode Core (packages/opencode)]
    API[Hono HTTP API + SSE/WebSocket]
    Session[Session + Message Store]
    LLM[LLM Stream Orchestrator]
    Tools[Tool Registry + MCP]
    Bus[Event Bus]
  end

  Storage[(Local JSON Storage)]
  FS[(Workspace Files)]
  Providers[(LLM Providers)]
  MCP[(MCP Servers)]

  CLI --> API
  SDK --> API
  Desktop --> API
  Web --> API

  API --> Session
  API --> Bus
  Session --> Storage
  LLM --> Providers
  LLM --> Tools
  Tools --> FS
  Tools --> MCP
  Bus --> API
```
