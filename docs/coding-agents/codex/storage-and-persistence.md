# Storage and persistence (“database”)

Codex does not use a traditional database. Persistence is file-based and lives
under the Codex home directory (typically `~/.codex`). The core mechanisms are
rollout JSONL files and a global history log.

## 1) Rollout files (per-thread persistence)

**What they are**

- Each thread is persisted as a JSONL file (“rollout”).
- One JSON object per line, containing a `RolloutItem` or session metadata.
- Used to resume, fork, list, and archive threads.

**Where they live**

- Directory layout:
  - `~/.codex/sessions/YYYY/MM/DD/rollout-YYYY-MM-DDThh-mm-ss-<uuid>.jsonl`
- Archived rollouts live in:
  - `~/.codex/archived_sessions/`

**Files to read**

- Writer: `src/coding-agents/codex/codex-rs/core/src/rollout/recorder.rs`
- Directory layout + listing: `src/coding-agents/codex/codex-rs/core/src/rollout/list.rs`
- Rollout constants: `src/coding-agents/codex/codex-rs/core/src/rollout/mod.rs`

**How they are written**

- `RolloutRecorder` is created during `Session::new` (in `codex.rs`) and stored
  in `SessionServices.rollout`.
- The recorder writes to disk after each update, flushing on request and during
  shutdown.
- `Session::persist_rollout_items` filters and persists events as
  `RolloutItem`s.

**How they are read**

- `RolloutRecorder::get_rollout_history` reads a rollout file into
  `InitialHistory`.
- `ThreadManager` uses this for `resume` and `fork`.
- App-server uses rollouts to implement thread list/resume/fork/archive.

**Thread listing**

- Thread listing scans rollout directories on disk and extracts summary data.
- There is no separate index. See `rollout/list.rs` and the
  `RolloutRecorder::list_threads` helpers.

## 2) Global message history (append-only)

**What it is**

- A single append-only JSONL file containing user-visible messages across
  threads.
- Each line is a JSON object with: `session_id`, `ts`, `text`.

**Location**

- `~/.codex/history.jsonl`

**File**

- `src/coding-agents/codex/codex-rs/core/src/message_history.rs`

**Notable behavior**

- Uses file locks and a single `write(2)` to avoid interleaving on concurrent
  writers.
- Supports size limits and trimming via `history.max_bytes`.

## 3) Config and auth (adjacent persistence)

While not a database, these files are part of persisted state:

- `config.toml` (settings) and any profiles under `~/.codex/`
- Auth and token storage (see `src/coding-agents/codex/codex-rs/login/`)

## App-server usage (why rollouts matter)

The app-server builds its “thread database” view directly from rollouts:

- `thread/list`: scans rollouts and returns summaries
- `thread/resume`: loads rollout history into a live session
- `thread/fork`: truncates rollout history then spawns a new session
- `thread/archive`: moves the rollout file to `archived_sessions`

Implementation lives in:

- `src/coding-agents/codex/codex-rs/app-server/src/codex_message_processor.rs`
- `src/coding-agents/codex/codex-rs/app-server/README.md`

## Takeaway

Persistence is **file-based** and intentionally simple:

- Rollouts = per-thread durability + resume
- history.jsonl = global lightweight log
- No DB; the “database” is the filesystem
