# Claude Code Open Architecture Overview

This overview summarizes the CLI/TUI runtime, session loop, and persistence
boundaries. It complements the per-subsystem notes.

## System overview

Diagram: `diagrams/system-overview.mmd`.

Key components:

- CLI/TUI entry points feed a shared `ConversationLoop`.
- The loop builds prompts, executes tools through permissions + hooks, and
  appends tool results back into session history.
- Sessions, summaries, memory, and telemetry are persisted under `~/.claude`.
- External dependencies include Anthropic APIs, OAuth flows, MCP servers, and
  web fetch/search tooling.
