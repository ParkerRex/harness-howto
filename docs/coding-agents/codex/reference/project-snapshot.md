# Codex Project Snapshot

## 0. Snapshot metadata

- Snapshot UTC: 2026-01-15T15:11:45Z
- Snapshot source ref (commit or tag): e1447c300
- Snapshot cadence (e.g., every 3 days): every 3 days (or manual dispatch)
- Snapshot scope (what changed triggers a snapshot): doc updates in `docs/coding-agents/codex/`

## 1. Doc set (existing artifacts)

- `README.md` - TL;DR, entry points, and where to start reading.
- `architecture-overview.md` - System-level summary + primary data/control flows.
- `key-patterns.md` - Repeated architectural patterns, conventions, and invariants.
- `comparison.md` - How this project differs from other agents in the repo.
- `onboarding.md` - Legacy stub that points to reference docs.
- `repo-map.md` - Legacy stub that points to reference docs.
- `CHANGELOG-upstream.md` - Upstream project changes since last sweep.
- `CHANGELOG-docs.md` - Documentation updates made in this repo.
- `reference/directory-map.md` - Top-level folders + what they own.
- `reference/file-tree.md` - Top-level file tree (tracked files, depth-limited).
- `reference/key-files.md` - High-leverage files for core flows.
- `reference/mental-model.md` - Mental model + suggested reading path.
- `reference/project-snapshot.md` - This snapshot (overview + gaps).
- `subsystems/` - Deep dives:
  - `core-runtime.md`
  - `models-prompting.md`
  - `tools.md`
  - `context-compaction.md`
  - `persistence.md`
  - `safety-failure-modes.md`
- `diagrams/` - Mermaid diagrams:
  - `system-overview.mmd`
  - `session-event-flow.mmd`
  - `tools-call-sequence.mmd`
  - `prompt-assembly.mmd`
  - `compaction-flow.mmd`

## 2. Tech stack

- Languages (primary + secondary): Rust (core), TypeScript/JavaScript (CLI wrapper, SDK, MCP), plus Markdown, Starlark (Bazel), and Nix.
- Runtimes/frameworks: Rust binaries for core/runtime + UI surfaces; Node.js for JS/TS tooling and MCP server.
- Build/package tooling: Cargo (Rust workspace), pnpm workspace (`package.json`, `pnpm-workspace.yaml`), Bazel (`BUILD.bazel`, `MODULE.bazel`), Nix (`flake.nix`), `justfile`.
- Infra/services (DB, queue, cache, external APIs): local filesystem persistence under `~/.codex`; model APIs configured by provider; optional MCP tool servers (including `shell-tool-mcp`).
- Observability (logs/metrics/tracing): JSONL session logs under `~/.codex`; UI event stream from core; no dedicated metrics/tracing doc in this repo.

## 3. Repo stats (as of 2026-01-15)

- Commits: 2830
- Contributors: 314
- Default branch: main
- License: Apache License 2.0
- Release cadence (tags or releases): tags present (rust-v* / codex-rs-*); latest reachable tag: `codex-rs-2925136536b06a324551627468d17e959afa18d4-1-rust-v0.2.0-alpha.2`

## 4. Repo shape & entry points (10–15 min)

- Entry points (where execution starts):
  - Frontends (TUI/exec CLI/SDK) submit `Op` requests to `ThreadManager`.
  - Core loop: `codex-rs/core/src/codex.rs` (`Session`, `run_turn`).
  - Thread/session wiring: `codex-rs/core/src/thread_manager.rs`.
  - Tool dispatch: `codex-rs/core/src/tools/router.rs`.
- "Happy path" flow (what a typical run does):
  - User input → `Op` → `Session` builds prompt → model response → tool calls (if any) → ToolRouter executes → outputs appended to history → events emitted to UI.
- Monorepo or single-purpose: monorepo (Rust core + JS/TS packaging + SDK + MCP server + docs).
- Key top-level dirs and why they matter:
  - `codex-rs/` (core runtime, protocol, tools, compaction, persistence)
  - `codex-cli/` (JS/TS packaging wrapper)
  - `sdk/` (SDKs for programmatic use)
  - `shell-tool-mcp/` (MCP server for shell tools)
  - `docs/` (user/dev docs)
- Build/run files (README, package.json, go.mod, Makefile, etc.): `README.md`, `PNPM.md`, `package.json`, `pnpm-workspace.yaml`, `BUILD.bazel`, `MODULE.bazel`, `justfile`, `flake.nix`.

## 5. Repo file tree (top-level)

- `reference/file-tree.md` (tracked files, depth-limited)

## 6. Architecture in one whiteboard diagram

- Diagram: `diagrams/system-overview.mmd`
- Flow: User/Event → Entry Point → Core Logic → Side Effects
- Boundaries (where data changes shape):
  - Frontend ↔ core via `Op`/`Event` protocol (`codex-rs/protocol`).
  - Core ↔ model API via model clients (prompt assembly + response parsing).
  - Core ↔ tools via `ToolRouter` and MCP connection manager.
  - Core ↔ persistence via JSONL session logs under `~/.codex`.
- Stateful vs stateless parts:
  - Stateful: `Session` (history, features, tool configs).
  - Stateless-ish: tool handlers (per-call execution), frontends render events.
- Sync vs async:
  - Turn loop is sequential; tool calls/MCP IO are async-bound within a turn.
- Hot paths vs background work:
  - Hot: `run_turn`, context history management, ToolRouter dispatch.
  - Background: session persistence, MCP connection management.

## 7. Data flow > code flow

- Where data enters the system: frontends submit `Op` requests (`codex-rs/protocol`).
- Validation → transformation → persistence → response:
  - Input normalized to `ResponseItem` timeline → prompt assembly → model output parsed → tool execution outputs → history persisted to JSONL → events emitted.
- Domain objects vs DTOs:
  - DTOs: `Op`/`Event` (client protocol), `ResponseItem` (model API shape).
  - Domain: `Session`, `Turn`, `ToolRouter` (core runtime).
- Where schemas live: `codex-rs/protocol/src/protocol.rs` and `codex-rs/protocol/src/models.rs`.
- Ownership (who "owns" each piece of data): core owns session state; frontends own UI state; tools own side-effect execution.
- Where data mutates: context manager history + compaction (`codex-rs/core/src/context_manager`).
- What assumptions are baked in: bounded context window, tool gating via exec policy approvals.

## 8. Tests tell you what matters

- What has tests vs what doesn't: unit tests in Rust crates (e.g., `codex-rs/file-search`, `codex-rs/ollama`, `codex-rs/utils/absolute-path`).
- Edge cases covered: parser/URL handling and file-search utilities; core runtime tests are limited in this snapshot.
- Mock-heavy zones (signals slowness/instability): not called out in docs; inspect per-crate tests.
- Snapshot vs behavioral tests: primarily unit/behavioral tests in Rust crates; no snapshot tests noted here.
- What breaks most often (if known): see safety/failure modes for runtime guardrails.

## 9. Error handling & logging

- Structured logging or ad-hoc logs: session activity persisted as JSONL under `~/.codex`.
- Centralized error handling: core emits failure `ResponseItem` for tool errors instead of crashing turns.
- Retry logic / timeouts / circuit breakers: handled in tool/MCP layers; see `mcp_connection_manager` for error surfaces.
- If this breaks in prod, what do we see?
  - Events emitted to frontends; session logs capture tool failures, compaction issues, MCP failures, and invalid input warnings.

## 10. Observability & operations (optional)

- Metrics/tracing tooling: not documented in the manual set.
- Feature flags / env config: exec policy + tool approvals; config persisted under `~/.codex`.
- Deployment shape (local, cloud, etc.): local CLI/runtime; optional MCP servers.

## 11. Sharp edges & footguns

- Global state: session state lives in-memory per thread; history persisted to JSONL.
- Hidden side effects: tool execution and MCP calls can mutate external state.
- Implicit conventions: tool names sanitized/truncated for API constraints.
- Magic env flags: exec policy and sandbox approvals govern tool access.
- Temporal coupling (this must run before that):
  - Compaction must fit context window before the next model call.
  - Undo/ghost snapshots require git state and can fail with large untracked files.
