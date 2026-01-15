# Codex Mental Model

Paths are relative to the `src/coding-agents/codex/` submodule.

## How to explain Codex to a new engineer

Codex CLI is a Rust-first agent runtime. A thread owns a session that holds all
state (history, features, tool configs). Each user input starts a turn: Codex
builds a prompt from history plus instructions, sends it to a model, and either
gets a message or a tool call. Tool calls are routed through a registry and
executed in a sandbox; outputs are recorded back into history and fed into the
next model turn. The system keeps context bounded via truncation and compaction,
and it persists every session as JSONL so it can be resumed or audited.

## Suggested onboarding exercises

1) Tool path walk
   - Follow a tool call from `ResponseItem::FunctionCall` -> `ToolRouter`
     -> `ToolHandler` -> output.

2) Compaction walk
   - Read `compact.rs` and `compact_remote.rs` and map to the `run_turn` loop.

3) Session persistence walk
   - Trace `RolloutRecorder` and how a session is resumed.

4) Prompt assembly walk
   - Map `ModelInfo` -> `Prompt` -> `ModelClient` -> response items.
