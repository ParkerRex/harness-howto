# Core Runtime (Threads, Sessions, Turns)

This subsystem is the heart of Codex: it owns threads, sessions, the turn loop,
and the event stream that frontends render.

## Threads, sessions, turns

- Thread: externally exposed conversation handle.
  - `codex-rs/core/src/thread_manager.rs` creates threads and stores them in memory.
  - `codex-rs/core/src/codex_thread.rs` exposes a lightweight handle for ops/events.

- Session: internal stateful agent runtime for a thread.
  - `codex-rs/core/src/codex.rs` (struct `Session`)
  - Owns history, features, services (MCP, exec policy), and the active turn.

- Turn: one cycle of prompt -> model response(s) -> tools -> completion.
  - `codex-rs/core/src/codex.rs` (`run_turn`, `run_model_turn`)

## Response items (canonical transcript)

History is recorded as `ResponseItem` values (model API shapes). Tools and
messages are normalized into a single timeline.

- `codex-rs/protocol/src/models.rs` - `ResponseItem` and content structures.
- `codex-rs/core/src/context_manager/history.rs` - in-memory storage.
- `codex-rs/core/src/event_mapping.rs` - parses `ResponseItem` into `TurnItem`
  for UI/event rendering.

## Events + ops (client <-> core contract)

- `codex-rs/protocol/src/protocol.rs` defines `Op` (requests) and `Event` (responses).
  Examples: `Op::Compact`, `Op::Undo`, `Op::ThreadRollback`, `Op::ListSkills`,
  `Op::RunUserShellCommand`.

## Session lifecycle and event flow (swimlane)

Diagram: `../diagrams/session-event-flow.mmd`.

Where to look:
- Turn loop: `codex-rs/core/src/codex.rs` (`run_turn`, `run_model_turn`)
- Event emission: `codex-rs/core/src/codex.rs` (`send_event` calls)
- Turn item parsing: `codex-rs/core/src/event_mapping.rs`
