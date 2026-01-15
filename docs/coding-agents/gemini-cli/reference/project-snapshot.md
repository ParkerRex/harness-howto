# Gemini CLI Project Snapshot

## 0. Snapshot metadata

- Snapshot UTC: 2026-01-15T16:34:52Z
- Snapshot source ref (commit or tag): dfb7dc706
- Snapshot cadence (e.g., every 3 days): every 3 days (or manual dispatch)
- Snapshot scope (what changed triggers a snapshot): doc updates in `docs/coding-agents/gemini-cli/`

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
- `reference/key-files.md` - High-leverage files for core flows.
- `reference/mental-model.md` - Mental model + suggested reading path.
- `reference/project-snapshot.md` - This snapshot (overview + gaps).
- `subsystems/` - Deep dives:
  - `core-runtime.md`
  - `tools.md`
  - `models-prompting.md`
  - `context-compaction.md`
  - `persistence-sessions.md`
  - `safety-failure-modes.md`
  - `observability.md`
- `diagrams/` - Mermaid diagrams:
  - `system-overview.mmd`
  - `tools-state-machine.mmd`
  - `compaction-flow.mmd`

Notes: a top-level file tree reference has not been captured yet.

## 2. Tech stack

- Languages (primary + secondary): TypeScript/JavaScript (CLI/UI/Core, A2A server, IDE companion), plus Markdown, JSON, and shell scripts.
- Runtimes/frameworks: Node.js runtime; Ink/React for the CLI UI; VS Code extension runtime for the IDE companion.
- Build/package tooling: npm workspaces (`package.json`, `package-lock.json`), TypeScript (`tsconfig.json`), esbuild (`esbuild.config.js`), Vitest, ESLint, Prettier.
- Infra/services (DB, queue, cache, external APIs): local storage under `~/.gemini`; optional sandboxing via Seatbelt (macOS) or Docker/Podman; external Gemini model API via `GeminiChat`; optional A2A HTTP server.
- Observability (logs/metrics/tracing): telemetry helpers under `packages/core/src/telemetry`.

## 3. Repo stats (as of 2026-01-15)

- Commits: 3963
- Contributors: 490
- Default branch: main
- License: Apache License 2.0
- Release cadence (tags or releases): tags present (nightly/preview); latest reachable tag: `v0.1.12`

## 4. Repo shape & entry points (10-15 min)

- Entry points (where execution starts):
  - CLI entry + interactive startup: `packages/cli/src/gemini.tsx`.
  - Non-interactive CLI mode: `packages/cli/src/nonInteractiveCli.ts`.
  - A2A server HTTP surface: `packages/a2a-server/src/http/app.ts`.
  - VS Code companion extension entry: `packages/vscode-ide-companion/src/extension.ts`.
- "Happy path" flow (what a typical run does):
  - User input -> config/policy wiring -> `GeminiClient.sendMessageStream` -> `GeminiChat` streams responses -> `Turn.run` extracts tool calls -> `CoreToolScheduler` validates/executes -> `ToolExecutor` runs tools -> `ChatRecordingService` persists session -> telemetry/UI updates.
- Monorepo or single-purpose: monorepo (CLI UX, core runtime, A2A server, IDE companion).
- Key top-level dirs and why they matter:
  - `packages/cli/` (Ink UI, CLI commands, sandbox bootstrap)
  - `packages/core/` (runtime, prompts, tools, policy, telemetry)
  - `packages/a2a-server/` (HTTP agent interface)
  - `packages/vscode-ide-companion/` (IDE integration)
  - `integration-tests/` (end-to-end flows + sandbox coverage)
  - `evals/` (eval harness)
  - `scripts/` (build/lint/release helpers)
  - `schemas/` (settings and config schemas)
  - `docs/` (upstream project docs)
- Build/run files (README, package.json, go.mod, Makefile, etc.): `README.md`, `GEMINI.md`, `package.json`, `package-lock.json`, `tsconfig.json`, `esbuild.config.js`, `Makefile`, `Dockerfile`.

## 5. Repo file tree (top-level)

- Not captured yet for Gemini CLI; consider generating `reference/file-tree.md` (tracked files, depth-limited).

## 6. Architecture in one whiteboard diagram

- Diagram: `diagrams/system-overview.mmd`
- Flow: CLI/IDE/A2A input -> Core runtime -> Model API -> Tools/Policy/Sandbox -> Persistence + Telemetry -> UI/clients
- Boundaries (where data changes shape):
  - CLI <-> Core via config, streaming events, and confirmation prompts.
  - Core <-> model via `GeminiChat`.
  - Core <-> tools via `CoreToolScheduler` and `ToolExecutor`.
  - Core <-> persistence via `ChatRecordingService`.
- Stateful vs stateless parts:
  - Stateful: session history + settings under `~/.gemini`, in-memory turn state.
  - Stateless-ish: tool executors and model calls per turn.
- Sync vs async:
  - Turn loop is sequential; model streaming + tool execution are async within a turn.

## 7. Data flow > code flow

- Where data enters the system: CLI input (interactive/non-interactive) and A2A HTTP requests.
- Validation -> transformation -> persistence -> response:
  - Input normalized -> prompt assembly -> model stream -> tool extraction/execution -> message records appended -> session JSON persisted -> UI + telemetry updates.
- Domain objects vs DTOs:
  - Domain: `ConversationRecord`, `MessageRecord`, `ToolCallRecord`.
  - DTOs: model request/response payloads and tool invocation schemas.
- Where schemas live: `packages/core/src/services/chatRecordingService.ts` and `packages/core/src/scheduler/types.ts`.
- Ownership (who "owns" each piece of data): Core owns session + tool lifecycle; CLI owns render/interaction state.
- Where data mutates: `GeminiClient.processTurn`, `Turn.run`, `CoreToolScheduler`, `ChatCompressionService`, `ChatRecordingService`.
- What assumptions are baked in: bounded token limits; tool calls gated by policy; sessions stored under `~/.gemini/tmp/<project_hash>/chats/`.

## 8. Tests tell you what matters

- What has tests vs what doesn't: unit tests under `packages/*/src/*.test.ts`; integration tests under `integration-tests/`; script tests under `scripts/tests/`; eval runs under `evals/`.
- Edge cases covered: A2A server endpoints/commands, CLI initialization/settings, IDE companion behavior, sandboxed integration flows.
- Mock-heavy zones (signals slowness/instability): CLI test utilities and service tests use mocks (see `packages/cli/src/test-utils/`).
- Snapshot vs behavioral tests: mostly behavioral/unit tests with Vitest; integration tests exercise full CLI flows.
- What breaks most often (if known): not called out; see safety/failure modes for known failure classes.

## 9. Error handling & logging

- Structured logging or ad-hoc logs: telemetry events + session JSON capture tool calls, errors, and token usage.
- Centralized error handling: `GeminiChat` retries invalid streams; `PolicyEngine` gates confirmations; `ToolExecutor` wraps failures as structured tool responses.
- Retry logic / timeouts / circuit breakers: retries in `packages/core/src/core/geminiChat.ts`; fallback handling in `packages/core/src/fallback/handler.ts`.
- If this breaks in prod, what do we see?
  - CLI error output, telemetry events, and session records under `~/.gemini/tmp/<project_hash>/chats/`.

## 10. Observability & operations (optional)

- Metrics/tracing tooling: telemetry and Clearcut logger under `packages/core/src/telemetry`.
- Feature flags / env config: `GEMINI_SANDBOX` or `--sandbox` for sandboxing; `GEMINI_SYSTEM_MD` overrides system prompt; `GEMINI_WRITE_SYSTEM_MD` writes the built-in prompt; settings under `~/.gemini`.
- Deployment shape (local, cloud, etc.): local CLI; optional Docker/Podman sandbox; optional A2A server and VS Code companion.

## 11. Sharp edges & footguns

- Global state: session + settings persisted under `~/.gemini`; in-memory turn state in Core.
- Hidden side effects: tools can execute shell commands; optional checkpointing can create git snapshots.
- Implicit conventions: tool lifecycle state machine and policy confirmations must stay in sync with UI expectations.
- Magic env flags: `GEMINI_SANDBOX`, `GEMINI_SYSTEM_MD`, `GEMINI_WRITE_SYSTEM_MD`.
- Temporal coupling (this must run before that): compaction must run before token overflow; tool state transitions must remain ordered for streaming UI.
