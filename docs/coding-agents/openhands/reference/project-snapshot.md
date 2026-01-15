# OpenHands Project Snapshot

## 0. Snapshot metadata

- Snapshot UTC: 2026-01-15T16:30:48Z
- Snapshot source ref (commit or tag): 6ccd42bb2
- Snapshot cadence (e.g., every 3 days): every 3 days (or manual dispatch)
- Snapshot scope (what changed triggers a snapshot): doc updates in `docs/coding-agents/openhands/`

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
  - `tools-runtime.md`
  - `models-prompting.md`
  - `context-compaction.md`
  - `persistence-sessions.md`
  - `safety-failure-modes.md`
  - `observability.md`
- `diagrams/` - Mermaid diagrams:
  - `system-overview.mmd`

## 2. Tech stack

- Languages (primary + secondary): Python (agent/runtime/app server), TypeScript/JavaScript (frontend/CLI), plus Markdown, shell scripts, and Docker/YAML.
- Runtimes/frameworks: FastAPI for the V1 app server, React for the UI, Python runtime for agent loop/tools, Node tooling for frontend assets.
- Build/package tooling: Poetry (`pyproject.toml`, `poetry.lock`), Makefile, Docker/Docker Compose, and assorted scripts under `scripts/`.
- Infra/services (DB, queue, cache, external APIs): event store + filesystem persistence; runtime abstraction supports Docker/local/remote execution; external model providers configured via runtime/LLM config.
- Observability (logs/metrics/tracing): metrics and telemetry helpers under `openhands/llm/` and `openhands/server/` (see observability subsystem).

## 3. Repo stats (as of 2026-01-15)

- Commits: 5869
- Contributors: 454
- Default branch: main
- License: MIT for core; enterprise directory has its own license
- Release cadence (tags or releases): tags present; latest reachable tag: `1.1.0`

## 4. Repo shape & entry points (10–15 min)

- Entry points (where execution starts):
  - V1 app server routing: `openhands/app_server/v1_router.py`.
  - Legacy V0 websocket server: `openhands/server/listen.py` and `openhands/server/session/session.py`.
  - Agent controller + loop: `openhands/controller/agent_controller.py` and `openhands/agenthub/codeact_agent/codeact_agent.py`.
- "Happy path" flow (what a typical run does):
  - User request → app server/session → controller plans actions → runtime executes tools → events persisted → UI updated.
- Monorepo or single-purpose: monorepo (agent runtime, server, UI, CLI, enterprise extensions).
- Key top-level dirs and why they matter:
  - `openhands/` (core Python runtime, controller, agenthub, server)
  - `frontend/`, `openhands-ui/` (web UI)
  - `openhands-cli/` (CLI packaging)
  - `enterprise/` (source-available enterprise extensions)
  - `evaluation/` (benchmarks and eval harness)
- Build/run files (README, package.json, go.mod, Makefile, etc.): `README.md`, `pyproject.toml`, `poetry.lock`, `Makefile`, `docker-compose.yml`.

## 5. Repo file tree (top-level)

- `reference/file-tree.md` (tracked files, depth-limited)

## 6. Architecture in one whiteboard diagram

- Diagram: `diagrams/system-overview.mmd`
- Flow: Client/UI → App Server → Controller/Agent → Runtime/Tools → Event Store → UI
- Boundaries (where data changes shape):
  - API requests ↔ controller/agent state.
  - Tool calls ↔ runtime execution (local/docker/remote).
  - Events ↔ persistence/streaming.
- Stateful vs stateless parts:
  - Stateful: controller sessions, event store, conversation state.
  - Stateless-ish: tool execution workers per action.
- Sync vs async:
  - Controller loop is sequential; runtime actions and streaming are async.

## 7. Data flow > code flow

- Where data enters the system: HTTP/websocket requests to app server or CLI inputs.
- Validation → transformation → persistence → response:
  - Request → controller builds actions → runtime executes → events emitted → persisted → streamed to clients.
- Domain objects vs DTOs:
  - Domain: controller state, events, actions, conversations.
  - DTOs: API request/response payloads, runtime action schemas.
- Where schemas live: API models under `openhands/app_server/` and event types under `openhands/events/`.
- Ownership (who "owns" each piece of data): app server owns sessions; controller owns agent state; runtime owns tool execution output.
- Where data mutates: controller loop + event stream; runtime action results enrich events.
- What assumptions are baked in: multi-runtime support, dual V0/V1 stacks, tool actions as first-class events.

## 8. Tests tell you what matters

- What has tests vs what doesn't: tests live under `tests/` with runtime, server, and tool coverage.
- Edge cases covered: runtime sandboxing, tool execution, agent/controller flows (see subsystem notes).
- Mock-heavy zones (signals slowness/instability): integration tests frequently mock providers/runtimes.
- Snapshot vs behavioral tests: mostly behavioral/integration-style; some snapshot-like golden files in eval outputs.
- What breaks most often (if known): runtime/tool integration and provider edges; see safety/failure docs.

## 9. Error handling & logging

- Structured logging or ad-hoc logs: service logs and event stream logs; metrics helpers in `openhands/llm/metrics.py`.
- Centralized error handling: controller captures tool/runtime failures and emits error events.
- Retry logic / timeouts / circuit breakers: handled in runtime and provider layers; see safety/failure docs.
- If this breaks in prod, what do we see?
  - Error events in the event stream; server logs and metrics counters.

## 10. Observability & operations (optional)

- Metrics/tracing tooling: telemetry and metrics helpers under `openhands/llm/` and `openhands/server/`.
- Feature flags / env config: environment-based config via templates (`config.template.toml`) and runtime settings.
- Deployment shape (local, cloud, etc.): local dev with docker-compose; optional remote runtimes; UI served separately.

## 11. Sharp edges & footguns

- Global state: controller/session state persists across events and can drift between V0 and V1 paths.
- Hidden side effects: tool actions mutate external state; runtime isolation depends on backend.
- Implicit conventions: event/action schemas must remain stable across controller/runtime/UI.
- Magic env flags: runtime configuration and model/provider settings in config files/env.
- Temporal coupling (this must run before that): controller must persist events before UI consumes; runtime actions must finish before next agent turn.
