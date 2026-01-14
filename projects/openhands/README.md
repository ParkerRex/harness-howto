# OpenHands Architecture Docs (Manual)

TL;DR: This directory is a manual, per-subsystem breakdown of the OpenHands
codebase (the `openhands` submodule). It covers the legacy V0 agent loop and the
V1 app server that fronts external SDK/tools, plus runtimes, events, and
persistence. Paths below are relative to the `openhands/` submodule.

## Start here

- Repo map: `repo-map.md`
- Core runtime (V0 loop + V1 app server): `subsystems/core-runtime.md`
- Tools and runtime actions: `subsystems/tools-runtime.md`
- Model selection + prompting: `subsystems/models-prompting.md`
- Context window + compaction: `subsystems/context-compaction.md`
- Sessions + persistence: `subsystems/persistence-sessions.md`
- Safety + failure modes: `subsystems/safety-failure-modes.md`
- Observability: `subsystems/observability.md`
- Onboarding checklist: `onboarding.md`

## System overview

```mermaid
flowchart LR
  subgraph Clients
    CLI[CLI]
    WebUI[Local Web UI]
    IDE[IDE/VSCode]
  end

  subgraph AppServerV1[App Server V1 (FastAPI)]
    V1Router[v1_router]
    AppConv[app_conversation]
    EventSvc[event service]
    SandboxSvc[sandbox service]
    UserSvc[user + jwt]
  end

  subgraph AgentCoreV1[Agent SDK / Tools (external)]
    SDK[openhands-sdk]
    Tools[openhands-tools]
    AgentServer[openhands-agent-server]
  end

  subgraph LegacyV0[Legacy V0 Core]
    ServerWS[server (websocket)]
    AgentCtl[controller + agenthub]
    Memory[memory + condenser]
    Events[event stream/store]
  end

  subgraph Runtime
    RuntimeClient[Runtime client]
    ActionExec[action_execution_server]
    Sandbox[Docker/Local/Remote]
  end

  LLM[LLM Providers]
  Store[FileStore / EventStore]

  Clients --> AppServerV1
  Clients --> ServerWS

  AppServerV1 --> SDK
  SDK --> AgentServer
  SDK --> Tools

  ServerWS --> AgentCtl
  AgentCtl --> Memory
  AgentCtl --> Events
  Memory --> Events

  AgentCtl --> LLM
  SDK --> LLM

  AgentCtl --> RuntimeClient
  RuntimeClient --> ActionExec
  ActionExec --> Sandbox

  Events --> Store
  AppServerV1 --> Store
```
