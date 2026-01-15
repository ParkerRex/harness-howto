# OpenCode Project Snapshot

## 0. Snapshot metadata

- Snapshot UTC: 2026-01-15T15:28:55Z
- Snapshot source ref (commit or tag): 44d24d42b
- Snapshot cadence (e.g., every 3 days): every 3 days (or manual dispatch)
- Snapshot scope (what changed triggers a snapshot): doc updates in `docs/coding-agents/opencode/`

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
  - `persistence-sharing.md`
- `diagrams/` - Mermaid diagrams:
  - `system-overview.mmd`
  - `message-flow.mmd`
  - `tool-state-chart.mmd`
  - `compaction-flow.mmd`

## 2. Tech stack

- Languages (primary + secondary): TypeScript/JavaScript (core runtime, CLI, UI, Cloudflare worker), plus Markdown and JSON; Rust for the Tauri desktop wrapper.
- Runtimes/frameworks: Bun for CLI/server/runtime, SolidJS/Vite for web surfaces, Cloudflare Workers/Durable Objects for sharing, Tauri for desktop.
- Build/package tooling: Bun workspaces (`bunfig.toml`), Turborepo (`turbo.json`), SST (`sst.config.ts`), Vite, Nix (`flake.nix`).
- Infra/services (DB, queue, cache, external APIs): local JSON storage under `Global.Path.data`; model provider APIs configured per provider; Cloudflare Durable Objects in `packages/function` for sharing/sync.
- Observability (logs/metrics/tracing): log files under `Global.Path.log`; no dedicated metrics/tracing doc in this manual set.

## 3. Repo stats (as of 2026-01-15)

- Commits: 7224
- Contributors: 593
- Default branch: dev
- License: MIT License
- Release cadence (tags or releases): tags present; latest reachable tag: `v1.1.20`

## 4. Repo shape & entry points (10–15 min)

- Entry points (where execution starts):
  - CLI entry + command wiring: `packages/opencode/src/index.ts`.
  - Server API surface: `packages/opencode/src/server/server.ts`.
  - Session loop + prompt execution: `packages/opencode/src/session/*`.
  - Share backend: `packages/function/src/api.ts`.
- "Happy path" flow (what a typical run does):
  - User input (CLI/TUI/HTTP) → Session builds prompt → LLM stream → tool calls → tool state updates → assistant message finalized → local storage + UI event stream.
- Monorepo or single-purpose: monorepo (core runtime, plugins, SDK, Cloudflare worker, desktop wrapper, web/console UIs).
- Key top-level dirs and why they matter:
  - `packages/opencode/` (core runtime, sessions, tools, server)
  - `packages/plugin/` (plugin hooks + tool injection)
  - `packages/sdk/` (OpenAPI schema + generated SDK)
  - `packages/function/` (share/sync backend)
  - `packages/desktop/`, `packages/console/`, `packages/web/` (desktop + web surfaces)
  - `specs/` (design notes)
- Build/run files (README, package.json, go.mod, Makefile, etc.): `README.md`, `package.json`, `bunfig.toml`, `turbo.json`, `sst.config.ts`, `flake.nix`.

## 5. Repo file tree (top-level)

- `reference/file-tree.md` (tracked files, depth-limited)

## 6. Architecture in one whiteboard diagram

- Diagram: `diagrams/system-overview.mmd`
- Flow: Client → Server Session Loop → Tool + Provider Execution → Persistence → Clients
- Boundaries (where data changes shape):
  - Client input ↔ `MessageV2`/parts model in session.
  - Session ↔ provider API via `session/llm.ts`.
  - Session ↔ tools via `tool/registry.ts` + `tool/*`.
  - Session ↔ persistence via `storage/storage.ts`.
- Stateful vs stateless parts:
  - Stateful: `Session` (message history, permissions, agent state).
  - Stateless-ish: tool executors (per-call), providers (per-request).
- Sync vs async:
  - Prompt loop is sequential; tool and provider IO are async within a turn.

## 7. Data flow > code flow

- Where data enters the system: CLI/TUI/HTTP inputs become `MessageV2` parts in sessions.
- Validation → transformation → persistence → response:
  - Input normalized → prompt assembly → model output parsed → tool execution → message parts updated → persisted to local JSON → streamed to UI.
- Domain objects vs DTOs:
  - Domain: `Session`, `MessageV2`, `ToolPart`.
  - DTO-ish: provider request/response payloads and share API data.
- Where schemas live: `packages/opencode/src/session/message-v2.ts`, `packages/opencode/src/tool/tool.ts`, `packages/opencode/src/provider/provider.ts`.
- Ownership (who "owns" each piece of data): server/session layer owns session state; clients own UI state; share backend owns remote session copies.
- Where data mutates: tool state machine in `session/processor.ts`, compaction in `session/compaction.ts`.
- What assumptions are baked in: bounded context windows, tool registry composition, optional share backend.

## 8. Tests tell you what matters

- What has tests vs what doesn't: tests live under `packages/opencode/test/` for sessions, tools, providers, permissions, CLI, snapshots, and config.
- Edge cases covered: tool truncation/patching, compaction/retry flows, permissions, provider transforms.
- Mock-heavy zones (signals slowness/instability): provider tests rely on fixtures/mocks; integration coverage appears limited.
- Snapshot vs behavioral tests: snapshot tests for tool output; behavioral tests for session/tool/permission flows.
- What breaks most often (if known): check compaction + tool state tests for regression signals.

## 9. Error handling & logging

- Structured logging or ad-hoc logs: structured log lines via `Log` with files under `Global.Path.log`.
- Centralized error handling: `NamedError` + `FormatError` for user-facing CLI errors; tool errors captured as tool parts.
- Retry logic / timeouts / circuit breakers: retry handling in session tests (`retry.test.ts`); provider/tool logic controls timeouts per tool.
- If this breaks in prod, what do we see?
  - CLI prints formatted errors; log files capture details + stack traces.

## 10. Observability & operations (optional)

- Metrics/tracing tooling: not documented in the manual set.
- Feature flags / env config: config stored under XDG config/data paths via `Global.Path`; `OPENCODE_TEST_HOME` overrides paths for tests.
- Deployment shape (local, cloud, etc.): local CLI/server; optional Cloudflare worker for share/sync.

## 11. Sharp edges & footguns

- Global state: session state lives in memory plus local JSON storage.
- Hidden side effects: tools and MCP integrations can mutate external state; share backend syncs remotely.
- Implicit conventions: message part types and tool state transitions must stay consistent for UI.
- Magic env flags: `OPENCODE_TEST_HOME` and `OPENCODE` environment variables.
- Temporal coupling (this must run before that): compaction must run before the next model call when context overflows; tool parts must transition in order.
