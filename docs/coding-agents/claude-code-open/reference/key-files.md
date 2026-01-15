# Claude Code Open Key Files

Paths are relative to the `src/coding-agents/claude-code-open/` submodule.

## Start here file list

- `src/cli.ts` - CLI flags and startup flow
- `src/core/loop.ts` - orchestration loop + tool execution + compaction
- `src/core/session.ts` - session state + persistence
- `src/session/index.ts` - session list/load/fork/merge
- `src/tools/base.ts` - tool base class + registry hooks
- `src/tools/index.ts` - tool registration
- `src/prompt/builder.ts` - system prompt assembly
- `src/models/config.ts` - model registry and context windows
- `src/context/index.ts` - token estimation + compression
- `src/permissions/index.ts` - permission modes + audit logging
- `src/auth/index.ts` - OAuth + API keys
- `src/ui/App.tsx` - Ink-based TUI
