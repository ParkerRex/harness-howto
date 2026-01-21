# Memory model (codex-core)

This doc describes what lives in memory while Codex runs, where it is stored,
its lifetime, and how it relates to disk persistence.

## Top-level in-memory objects

- **Thread registry** (`ThreadManagerState.threads`)
  - File: `src/coding-agents/codex/codex-rs/core/src/thread_manager.rs`
  - Type: `Arc<RwLock<HashMap<ThreadId, Arc<CodexThread>>>>`
  - Lifetime: process lifetime (unless explicitly removed)
  - Purpose: lookup active threads by id and route ops/events

- **Codex runtime** (`Codex`)
  - File: `src/coding-agents/codex/codex-rs/core/src/codex.rs`
  - Holds submission and event channels:
    - `tx_sub` (bounded channel of `Submission`)
    - `rx_event` (unbounded channel of `Event`)
  - A `CodexThread` simply wraps a `Codex` plus the rollout path.

## Session-scoped memory

A **Session** is the live runtime for a single thread. It owns:

- `SessionState` (mutable, protected by a mutex)
  - File: `src/coding-agents/codex/codex-rs/core/src/state/session.rs`
  - Fields:
    - `session_configuration` (current settings)
    - `history: ContextManager` (conversation transcript)
    - `latest_rate_limits` and token usage state
    - `server_reasoning_included` flag

- `SessionServices`
  - File: `src/coding-agents/codex/codex-rs/core/src/state/service.rs`
  - Contains shared services and managers:
    - MCP connection manager
    - Unified exec process manager
    - Rollout recorder handle (mutex-wrapped)
    - Approval store
    - Models manager, auth manager, skills manager
    - OTel manager and notification helper

These services are created in `Session::new` in
`src/coding-agents/codex/codex-rs/core/src/codex.rs`.

## Turn-scoped memory

Active work for a single user turn is tracked in `ActiveTurn`:

- File: `src/coding-agents/codex/codex-rs/core/src/state/turn.rs`
- `ActiveTurn` stores:
  - `tasks: IndexMap<String, RunningTask>` for in-flight work
  - `turn_state: Arc<Mutex<TurnState>>` for approvals and pending input

`TurnState` contains:

- `pending_approvals`: channels awaiting user approval
- `pending_user_input`: channels awaiting user answers
- `pending_input`: buffered `ResponseInputItem` values

Each running task is a `RunningTask` with cancellation tokens and handles.
This is what lets the session interrupt or cancel a turn.

## In-memory conversation history

- `ContextManager` (file:
  `src/coding-agents/codex/codex-rs/core/src/context_manager/history.rs`)
- Stores a vector of `ResponseItem`s (oldest → newest).
- Provides:
  - `record_items` to append new items
  - `for_prompt` to normalize and filter items before sending to the model
  - token estimation and truncation helpers

This is the **live memory** of the session and is what the model sees. It is
rebuilt on resume by replaying rollout items from disk.

## Event flow and buffering

- Each `Codex` runtime runs a submission loop (`submission_loop` in
  `codex.rs`) that consumes ops and emits events.
- Events flow through an **unbounded** channel to avoid dropping streaming
  output mid-turn.
- Submissions use a **bounded** channel to backpressure clients.

## Memory vs disk boundaries

- **Memory** holds current session state and the active thread map.
- **Disk** holds rollouts and global history. On resume or fork, codex-core
  reads rollouts from disk and reconstructs the `ContextManager` history.

Key crossover points:

- `Session::record_initial_history(...)` rehydrates in-memory history from
  rollout items (`codex.rs`).
- `Session::persist_rollout_items(...)` writes in-memory events to disk via the
  `RolloutRecorder`.

## Useful file references

- Session and runtime: `src/coding-agents/codex/codex-rs/core/src/codex.rs`
- Session state structs: `src/coding-agents/codex/codex-rs/core/src/state/*.rs`
- History management: `src/coding-agents/codex/codex-rs/core/src/context_manager/history.rs`
- Thread registry: `src/coding-agents/codex/codex-rs/core/src/thread_manager.rs`
