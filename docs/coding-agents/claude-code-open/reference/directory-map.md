# Claude Code Open Directory Map

At the top level of the `src/coding-agents/claude-code-open` submodule:

- `src/core/` - loop, session state, client calls, background tasks.
- `src/tools/` - tool implementations, registry, output persistence.
- `src/context/` - token estimation, window logic, compaction helpers.
- `src/prompt/` - system prompt templates and builder.
- `src/session/` - persistence, listing, resume, cleanup.
- `src/permissions/` - permission modes and audit logging.
- `src/auth/` - OAuth and API key flows.
- `src/ui/` - Ink/React TUI.
- `src/mcp/` - MCP client + registry.
- `src/sandbox/` - filesystem/network/process sandboxing.
- `src/telemetry/` - telemetry events and metrics.
