# Cline Architecture Docs (Manual)

TL;DR: This directory is a manual, per-subsystem breakdown of the Cline VS Code
extension. It focuses on the controller/task loop, tool execution with approvals,
prompt assembly, context/compaction, and disk persistence. Paths below are
relative to the `cline/` submodule.

## Start here

- Repo map: `repo-map.md`
- Core runtime (controller, task, messages): `subsystems/core-runtime.md`
- Tools + approvals: `subsystems/tools.md`
- Model selection + prompting: `subsystems/models-prompting.md`
- Context window + compaction: `subsystems/context-compaction.md`
- Persistence + history: `subsystems/persistence.md`
- Safety + failure modes: `subsystems/safety-failure-modes.md`
- Observability: `subsystems/observability.md`
- Onboarding checklist: `onboarding.md`

## System overview

```mermaid
flowchart LR
  U[User] -->|chat input| WV[Webview UI]

  subgraph VSCode[VS Code Extension Host]
    EXT[extension.ts]
    WVP[WebviewProvider]
    CTRL[Controller]
    TASK[Task]
    PROMPTS[System Prompt Registry]
    TOOLS[ToolExecutor + Handlers]
    CTX[ContextManager]
    STATE[StateManager]
  end

  subgraph Host[Host Integrations]
    TERM[Terminal Manager]
    FS[File System + Diff View]
    BROWSER[BrowserSession]
    MCP[MCP Hub]
  end

  subgraph Storage[Local Storage]
    TASKDIR[~/.cline/data/tasks/<taskId>]
    SETTINGS[Global/Workspace State + Secrets]
  end

  subgraph Providers[Model Providers]
    LLM[Anthropic/OpenAI/OpenRouter/etc]
  end

  U --> WV --> WVP --> CTRL --> TASK
  TASK --> PROMPTS
  TASK --> CTX
  TASK --> TOOLS
  TASK --> STATE
  TOOLS --> TERM
  TOOLS --> FS
  TOOLS --> BROWSER
  TOOLS --> MCP
  TASK --> LLM
  STATE --> SETTINGS
  TASK --> TASKDIR
```
