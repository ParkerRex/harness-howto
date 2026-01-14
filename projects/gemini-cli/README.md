# Gemini CLI Architecture Docs (Manual)

TL;DR: This directory is a manual, per-subsystem breakdown of the Gemini CLI
codebase (the `gemini-cli` submodule). It focuses on the CLI/Core split, the
turn loop, tool scheduling, policy gating, prompt/model routing, context
compression, and session persistence. Paths below are relative to the
`gemini-cli/` submodule.

## Start here

- Repo map: `repo-map.md`
- Core runtime (CLI/Core, turn loop, message model): `subsystems/core-runtime.md`
- Tools + policy + confirmations: `subsystems/tools.md`
- Model selection + prompting: `subsystems/models-prompting.md`
- Context window + compaction: `subsystems/context-compaction.md`
- Sessions + persistence: `subsystems/persistence-sessions.md`
- Failure modes + safety: `subsystems/safety-failure-modes.md`
- Observability: `subsystems/observability.md`
- Onboarding checklist + mental model: `onboarding.md`

## System overview

```mermaid
flowchart LR
  U[User] --> CLI

  subgraph CLI[CLI package (Ink UI + non-interactive)]
    CLIInput[Input + commands]
    CLIStream[Stream renderer]
    CLITools[Tool confirmations + output]
  end

  subgraph Core[@google/gemini-cli-core]
    Client[GeminiClient]
    Chat[GeminiChat]
    Turn[Turn event loop]
    Tools[CoreToolScheduler + ToolExecutor]
    Policy[PolicyEngine + MessageBus]
    Context[ContextManager]
    Registry[ToolRegistry + PromptRegistry]
    Hooks[HookSystem]
  end

  subgraph Ext[Extensions + MCP]
    MCP[MCP servers]
    ExtTools[Discovered tools/prompts/resources]
  end

  subgraph Storage[Local storage (~/.gemini + project temp)]
    Sessions[Session JSON + summaries]
    Settings[settings.json + policies]
    Memory[GEMINI.md + memory]
    Checkpoints[Tool checkpoints + git snapshots]
  end

  subgraph External[External services]
    GeminiAPI[Gemini API / Code Assist]
    Telemetry[Telemetry targets]
  end

  CLI --> Client
  Client --> Chat
  Chat --> GeminiAPI
  Turn --> Tools
  Tools --> Policy
  Context --> Client
  Registry --> Chat
  Hooks --> Chat

  MCP --> ExtTools
  ExtTools --> Registry

  Client --> Sessions
  Context --> Memory
  CLI --> Settings
  Tools --> Checkpoints
  Client --> Telemetry
```
