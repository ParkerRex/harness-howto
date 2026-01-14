# Codex Architecture Overview

This overview summarizes the Rust core, frontends, and the turn loop flow.

## System overview

Diagram: `diagrams/system-overview.mmd`.

Key components:

- Frontends (TUI, exec CLI, SDK) submit ops to `ThreadManager`.
- Sessions and turns live in `codex-core`, which owns context and tool routing.
- Tool calls are dispatched via the ToolRouter and MCP connection manager.
- Persistence is JSONL under `~/.codex`, with exec policy rules alongside.

## Turn loop summary

Agent loop in one sentence: a session receives user input, builds a prompt from
history plus instructions, asks the model to act, executes tool calls if
requested, records outputs back into history, and repeats until the turn ends.

Diagram: `diagrams/session-event-flow.mmd`.
