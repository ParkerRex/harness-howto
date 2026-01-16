# Claude Code Open Project Snapshot

## 0. Snapshot metadata

- Snapshot UTC: 2026-01-16T00:00:00Z
- Snapshot source ref (commit or tag): e1ed119
- Snapshot cadence (e.g., every 3 days): every 3 days (or manual dispatch)
- Snapshot scope (what changed triggers a snapshot): doc updates in `docs/coding-agents/claude-code-open/`

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
  - `tools.md`
  - `models-prompting.md`
  - `context-compaction.md`
  - `persistence-memory.md`
  - `safety-failure-modes.md`
  - `observability.md`

Notes: This is an educational reverse-engineering project recreating Claude Code CLI v2.1.4, not the official Anthropic source. The project includes a web UI mode (`web-cli.ts`) and blueprint/task-tree orchestration features not present in the official CLI.

## 2. Tech stack

- Languages (primary + secondary): TypeScript/JavaScript (CLI/TUI/Web UI), plus Markdown, JSON.
- Runtimes/frameworks: Node.js (>=18), React + Ink for terminal UI, Express for web server mode, Tree-sitter WASM for code parsing.
- Build/package tooling: npm, TypeScript (`tsc`), Vitest for testing, tsx for development.
- Infra/services (DB, queue, cache, external APIs): local filesystem persistence under `~/.claude`; Anthropic API for model calls; optional MCP servers; web fetch/search tools.
- Observability (logs/metrics/tracing): session logging to `~/.claude/sessions/`; telemetry helpers in `src/telemetry/`.

## 3. Repo stats (as of 2026-01-16)

- Commits: 236
- Contributors: 1 (single-author educational project)
- Default branch: main
- License: MIT
- Release cadence (tags or releases): version tracks official Claude Code (v2.1.4)

## 4. Repo shape & entry points (10-15 min)

- Entry points (where execution starts):
  - CLI entry: `src/cli.ts` (Commander.js argument parsing).
  - Web entry: `src/web-cli.ts` (Express server + WebSocket).
  - Main exports: `src/index.ts`.
- "Happy path" flow (what a typical run does):
  - CLI input -> `ConversationLoop` -> `ClaudeClient` (Anthropic API) -> tool execution via `ToolRegistry` -> session persistence -> UI output.
- Monorepo or single-purpose: single-purpose CLI/TUI application.
- Key top-level dirs and why they matter:
  - `src/` (core runtime, tools, agents, UI components, config, session management)
  - `scripts/` (utility scripts for development/testing)
  - `tests/` (unit, integration, and e2e tests)
- Build/run files (README, package.json, go.mod, Makefile, etc.): `README.md`, `CLAUDE.md`, `package.json`, `tsconfig.json`, `vitest.config.ts`.

## 5. Repo file tree (top-level)

- `reference/file-tree.md` (tracked files, depth-limited)

## 6. Architecture in one whiteboard diagram

- Diagram: `diagrams/system-overview.mmd`
- Flow: CLI Input -> ConversationLoop -> ClaudeClient (Anthropic API) -> ToolRegistry -> Session Persistence
- Boundaries (where data changes shape):
  - CLI/TUI input <-> conversation loop state.
  - Loop <-> Anthropic API via streaming responses.
  - Loop <-> tools via `ToolRegistry` and permission hooks.
  - Loop <-> persistence via session/summary/memory stores.
- Stateful vs stateless parts:
  - Stateful: session history, conversation state, permission caches.
  - Stateless-ish: tool executors (per-call), API client (per-request).
- Sync vs async:
  - Conversation loop is sequential; tool execution and API calls are async within turns.

## 7. Data flow > code flow

- Where data enters the system: CLI arguments, interactive prompts, web UI input.
- Validation -> transformation -> persistence -> response:
  - Input parsed -> prompt assembly in loop -> model response streamed -> tool calls extracted -> tool execution with permissions -> results appended to history -> session persisted -> UI rendered.
- Domain objects vs DTOs:
  - Domain: `Session`, `ConversationLoop`, `ToolRegistry`, tool implementations.
  - DTOs: Anthropic API request/response payloads, tool schemas.
- Where schemas live: `src/tools/` (Zod schemas per tool), `src/core/` (session types).
- Ownership (who "owns" each piece of data): loop owns conversation state; tools own execution logic; session manager owns persistence.
- Where data mutates: `ConversationLoop` history updates, session persistence writes, tool state changes.
- What assumptions are baked in: Anthropic API compatibility, tool permission model, bounded context windows.

## 8. Tests tell you what matters

- What has tests vs what doesn't: unit tests under `src/` (`.test.ts`), integration tests under `tests/integration/`, e2e CLI tests under `tests/e2e/`.
- Edge cases covered: tool execution paths, session management, agent communication patterns.
- Mock-heavy zones (signals slowness/instability): API client tests and tool tests use mocks.
- Snapshot vs behavioral tests: primarily behavioral tests with Vitest.
- What breaks most often (if known): API compatibility with official Claude Code, tool schema drift.

## 9. Error handling & logging

- Structured logging or ad-hoc logs: session files capture conversation history; telemetry helpers for event logging.
- Centralized error handling: loop captures tool/API errors and surfaces them in conversation.
- Retry logic / timeouts / circuit breakers: retry logic in `ClaudeClient`; configurable timeouts per tool.
- If this breaks in prod, what do we see?
  - CLI error output, session files with error states, failed tool results in conversation.

## 10. Observability & operations (optional)

- Metrics/tracing tooling: basic telemetry in `src/telemetry/`.
- Feature flags / env config: `ANTHROPIC_API_KEY`/`CLAUDE_API_KEY`, `USE_BUILTIN_RIPGREP`, `BASH_MAX_OUTPUT_LENGTH`, `CLAUDE_CODE_MAX_OUTPUT_TOKENS`.
- Deployment shape (local, cloud, etc.): local CLI; optional web server mode.

## 11. Sharp edges & footguns

- Global state: sessions persist to `~/.claude/sessions/` with 30-day expiry.
- Hidden side effects: tools can execute shell commands, write files, and call external APIs.
- Implicit conventions: tool registry pattern, permission hooks must be wired before tool execution.
- Magic env flags: API keys, ripgrep toggle, output limits.
- Temporal coupling (this must run before that): permission checks before tool execution; context management before model calls when hitting limits.
