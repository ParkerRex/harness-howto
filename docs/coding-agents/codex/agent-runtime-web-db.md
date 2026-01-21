# Agent Runtime + Web Frontend + Storage (Codex)

This doc explains how the Rust agent runtime (codex-core) is wired to a web
frontend via the app-server, and how persistence (“db”) works in the current
codebase.

## Big picture

The web UI does **not** talk to the model directly. It launches `codex app-server`
and speaks JSON-RPC over stdio. The app-server translates those requests into
codex-core operations (threads, turns, tools), streams events back to the UI,
and relies on codex-core’s rollout files for persistence.

```mermaid
flowchart LR
  UI[Web Frontend / Client UI]
  AS["codex app-server" (Rust)]
  CORE["codex-core runtime"]
  DB[(Rollout JSONL files)]

  UI <-- "JSON-RPC 2.0 over stdio" --> AS
  AS --> CORE
  CORE --> DB
```

Key files:

- App-server entry + IO loop: `codex-rs/app-server/src/lib.rs`
- JSON-RPC request handling: `codex-rs/app-server/src/message_processor.rs`
- App-server protocol + lifecycle: `codex-rs/app-server/README.md`
- Core runtime (agent loop, events): `codex-rs/core/src/codex.rs`
- Thread/session management: `codex-rs/core/src/thread_manager.rs`
- Persistence (rollouts): `codex-rs/core/src/rollout/recorder.rs`
- Rollout discovery + directory layout: `codex-rs/core/src/rollout/list.rs`

## Web frontend ↔ app-server contract

**Transport.** The app-server streams JSON-RPC messages over stdio, one JSON
object per line (JSONL). The UI starts the process, writes requests to stdin,
and reads notifications/responses from stdout. See the IO loop in
`codex-rs/app-server/src/lib.rs`.

**Handshake.** The first call must be `initialize`, followed by an
`initialized` notification; all other requests are rejected before that.
This is enforced in `codex-rs/app-server/src/message_processor.rs` and
documented in `codex-rs/app-server/README.md`.

**Primitives.** The app-server protocol exposes three primary objects:

- **Thread** (conversation)
- **Turn** (one user → agent exchange)
- **Item** (streamed sub-events within a turn: user input, model deltas, tool
  calls, shell output, etc.)

These are defined by the app-server protocol and described in
`codex-rs/app-server/README.md`. The protocol schemas can be generated via
`codex app-server generate-ts` / `generate-json-schema` (also in the README).

**Streaming events.** After `turn/start`, the UI receives JSON-RPC
notifications such as `turn/started`, `item/started`, deltas (e.g.
`item/agentMessage/delta`), and finally `turn/completed`. The app-server
mirrors codex-core events into these notifications.

**Approvals.** If a tool call (shell, file edit) needs approval, the app-server
initiates a JSON-RPC request back to the client. The UI must respond with an
approval decision before the turn can proceed (documented in the app-server
README under Approvals).

## App-server → Rust runtime wiring

The app-server owns a `ThreadManager` (from codex-core) and delegates all
conversation actions into it:

- `MessageProcessor` constructs a `ThreadManager` with `SessionSource::VSCode`
  and an `AuthManager`, then forwards JSON-RPC requests to
  `CodexMessageProcessor`.
- `CodexMessageProcessor` (in `codex-rs/app-server/src/codex_message_processor.rs`)
  translates protocol requests into codex-core thread/turn ops and streams
  results back to the client.

In codex-core, each thread is backed by a `Codex` instance (agent runtime) and
managed by `ThreadManager` (`codex-rs/core/src/thread_manager.rs`). The runtime
is a submit/stream loop: the UI submits ops (user turns, interrupts, etc.), and
codex-core emits events that the app-server forwards as JSON-RPC notifications.

## “DB” = rollout JSONL files

Codex does **not** use a database for conversation history. Persistence is file
based:

- Each thread is recorded as a JSONL **rollout** file via `RolloutRecorder`.
- Files live under `~/.codex/sessions/YYYY/MM/DD/` with names like
  `rollout-YYYY-MM-DDThh-mm-ss-<uuid>.jsonl`.
- The rollout file starts with session metadata, then appends `RolloutItem`
  entries as the turn progresses.

See:

- `codex-rs/core/src/rollout/recorder.rs` (writer + format)
- `codex-rs/core/src/rollout/list.rs` (directory layout + listing)
- `codex-rs/core/src/rollout/mod.rs` (sessions/archived directories)

The app-server uses these rollouts to implement:

- `thread/list` (scan rollouts and return summaries)
- `thread/resume` / `thread/fork` (load rollout history back into codex-core)
- `thread/archive` (move rollout file into the archived directory)

Those flows are implemented in `codex-rs/app-server/src/codex_message_processor.rs`
and rely on `RolloutRecorder::get_rollout_history` and helper functions like
`read_summary_from_rollout`.

## End-to-end flow (turn/start)

1. **UI starts app-server** and performs `initialize`.
2. **UI starts a thread** via `thread/start` (or resumes/forks an existing
   thread).
3. **codex-core creates a thread** and a `RolloutRecorder` starts a new JSONL
   file with session metadata.
4. **UI starts a turn** via `turn/start` with user input and optional config
   overrides.
5. **codex-core builds a prompt**, calls the model, executes tools, and emits
   events as the turn progresses.
6. **App-server streams events** to the UI as JSON-RPC notifications.
7. **RolloutRecorder appends items** (user input, model output, tool results)
   to the JSONL file as they occur.
8. **Turn ends** with `turn/completed` and token usage summaries.

## Where to look next

- Protocol types: `codex-rs/app-server-protocol/src/protocol/v2.rs`
- Event bridging: `codex-rs/app-server/src/bespoke_event_handling.rs`
- Rollout policies (which items persist): `codex-rs/core/src/rollout/policy.rs`
- Thread listing + archive details: `codex-rs/core/src/rollout/list.rs`
