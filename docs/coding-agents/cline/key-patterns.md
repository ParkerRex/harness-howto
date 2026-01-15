# Cline Key Patterns

- The Task object is the session boundary that owns prompts, tools, and
  persistence.
- Tool execution is gated by explicit approvals before handler dispatch.
- UI messages and provider messages are modeled as separate streams.
- Context management and compaction are centralized in `ContextManager`.
- Extension host integrations (terminal, diff view, browser, MCP) are surfaced
  as tools.
