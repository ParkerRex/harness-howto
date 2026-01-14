# Cline Onboarding

This page is the quick orientation for new engineers. Paths are relative to the
`cline/` submodule.

## Start here file list (most important)

- `src/extension.ts` - extension activation and command registration
- `src/core/webview/WebviewProvider.ts` - webview bootstrap and UI bridge
- `src/core/controller/index.ts` - controller orchestration
- `src/core/task/index.ts` - main task loop
- `src/core/task/ToolExecutor.ts` - tool execution + approvals
- `src/core/task/StreamResponseHandler.ts` - streaming tool parsing
- `src/core/context/context-management/ContextManager.ts` - context window logic
- `src/core/prompts/system-prompt/registry/PromptRegistry.ts` - prompt assembly
- `src/core/storage/StateManager.ts` and `src/core/storage/disk.ts` - persistence
- `src/services/mcp/McpHub.ts` - MCP server management

## Mental model cheat sheet

- Task = session boundary; the Task owns prompts, tools, and persistence.
- Two message streams: UI messages vs provider messages.
- Tools are defined in prompt specs and executed by ToolExecutor with approvals.
- ContextManager trims history and triggers compaction tools when needed.
- Everything runs locally inside the VS Code extension host process.
