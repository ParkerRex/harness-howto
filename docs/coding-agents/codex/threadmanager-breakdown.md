# ThreadManager breakdown

This doc explains how `ThreadManager` works in codex-core, with a file-by-file
breakdown and the key lifecycle calls.

## Primary file

- `src/coding-agents/codex/codex-rs/core/src/thread_manager.rs`

## What ThreadManager owns

- **In-memory thread registry**: `Arc<RwLock<HashMap<ThreadId, Arc<CodexThread>>>>`
  living inside `ThreadManagerState`. This is the authoritative map of active
  threads within the current process.
- **Thread creation broadcast**: `broadcast::Sender<ThreadId>` used by callers
  (like app-server) to attach listeners when new threads are created.
- **Shared managers**: `AuthManager`, `ModelsManager`, and `SkillsManager` are
  stored in the state so every thread has consistent access to auth, models, and
  skills.
- **Session source**: stored in `ThreadManagerState` and passed into each
  spawned session (e.g., `SessionSource::VSCode`).

## Key related files

- Thread wrapper: `src/coding-agents/codex/codex-rs/core/src/codex_thread.rs`
- Runtime spawn + session creation: `src/coding-agents/codex/codex-rs/core/src/codex.rs`
- Persistence (rollouts): `src/coding-agents/codex/codex-rs/core/src/rollout/recorder.rs`

## Lifecycle: start / resume / fork

ThreadManager exposes three main flows that all converge on `spawn_thread`:

1. **Start a new thread**
   - `ThreadManager::start_thread(config)`
   - Calls `ThreadManagerState::spawn_thread(config, InitialHistory::New, ...)`

2. **Resume from rollout**
   - `ThreadManager::resume_thread_from_rollout(config, rollout_path, ...)`
   - Loads rollout history from disk via
     `RolloutRecorder::get_rollout_history(&rollout_path)`
   - Calls `resume_thread_with_history`, which calls `spawn_thread` with
     `InitialHistory::Resumed`

3. **Fork**
   - `ThreadManager::fork_thread(nth_user_message, config, rollout_path)`
   - Loads rollout history, truncates before the nth user message using
     `rollout::truncation`, then spawns a new thread with
     `InitialHistory::Forked`

All three flows end up in:

- `ThreadManagerState::spawn_thread(...)`
  - Calls `Codex::spawn(...)` to construct a new runtime session
  - Immediately waits for the **first event** (`EventMsg::SessionConfigured`)
  - Wraps the returned runtime in a `CodexThread` (with the rollout path)
  - Inserts it into the thread map
  - Returns `NewThread { thread_id, thread, session_configured }`

## Sending work to a thread

- `ThreadManagerState::send_op(thread_id, op)`
  - Looks up the thread in the in-memory map
  - Delegates to `CodexThread::submit(op)`
  - Optional: logs ops in tests (`ops_log`)

`CodexThread` is a thin wrapper around `Codex` (the runtime), providing:

- `submit(op)` / `submit_with_id(submission)`
- `next_event()` (the event stream)
- `agent_status()`
- `rollout_path()` (for persistence/debugging)

## Thread removal and shutdown

- `ThreadManager::remove_thread(thread_id)` removes the entry from the map
- `ThreadManager::remove_and_close_all_threads()` sends `Op::Shutdown` to each
  thread, then clears the map
- Removing a thread does **not** guarantee destruction; `Arc<CodexThread>` may
  still be held elsewhere

## Broadcast: thread created

- `ThreadManagerState::notify_thread_created(thread_id)` sends the id to the
  broadcast channel
- Consumers (e.g., app-server) subscribe via
  `ThreadManager::subscribe_thread_created()` to attach new event listeners

## Failure modes and invariants

- The very first event from `Codex::spawn` must be
  `EventMsg::SessionConfigured`. If it is not, `spawn_thread` fails with
  `CodexErr::SessionConfiguredNotFirstEvent`.
- `get_thread(thread_id)` returns `CodexErr::ThreadNotFound` if the thread is
  not in the in-memory map.

## Quick trace: spawning a thread

- `ThreadManager::start_thread`
- `ThreadManagerState::spawn_thread`
- `Codex::spawn` (creates channels, builds session, spawns submission loop)
- First event = `SessionConfigured`
- `CodexThread::new` created with rollout path
- Thread inserted into the map

This division keeps ThreadManager focused on **lifecycle + registry**, while the
Codex runtime owns the agent loop, tool routing, and persistence mechanics.
