# Claude Code Open Key Patterns

- Multi-mode client (CLI/TUI/text) funnels through a single `ConversationLoop`.
- Tool execution is mediated by a PermissionManager and hook runner before
  hitting the ToolRegistry.
- Compaction uses a multi-step pipeline: microcompact, conversation summary, and
  optional session memory writeback.
- Local-first persistence keeps sessions, summaries, memory, telemetry, and tool
  outputs in separate stores under `~/.claude`.
