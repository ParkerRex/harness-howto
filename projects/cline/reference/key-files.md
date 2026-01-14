# Cline Key Files

Paths are relative to the `cline/` submodule.

## Start here file list

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
