# Cline Mental Model

Paths are relative to the `src/coding-agents/cline/` submodule.

## Cheat sheet

- Task = session boundary; the Task owns prompts, tools, and persistence.
- Two message streams: UI messages vs provider messages.
- Tools are defined in prompt specs and executed by ToolExecutor with approvals.
- ContextManager trims history and triggers compaction tools when needed.
- Everything runs locally inside the VS Code extension host process.
