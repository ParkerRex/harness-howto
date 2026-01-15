# Cline Architecture Overview

This overview summarizes the VS Code extension host runtime and how the task
loop connects UI, tools, and persistence.

## System overview

Diagram: `diagrams/system-overview.mmd`.

Key components:

- Webview UI posts chat input to the extension host.
- Controller and Task own the turn loop, prompt assembly, and tool execution.
- ToolExecutor routes tool calls to handlers and MCP integrations.
- ContextManager and StateManager bound history, compaction, and storage.
- Task data is persisted under `~/.cline/data/tasks/<taskId>`.
