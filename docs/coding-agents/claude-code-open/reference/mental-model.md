# Claude Code Open Mental Model

Paths are relative to the `src/coding-agents/claude-code-open/` submodule.

## Cheat sheet

- Loop = build system prompt -> call model -> run tools -> append tool results.
- Sessions are persisted JSON with usage stats and metadata.
- Permissions gate tools and can remember allow/deny decisions.
- Context pressure triggers auto-compaction.
- Storage is local by default: sessions, memory, telemetry, and tool outputs.
