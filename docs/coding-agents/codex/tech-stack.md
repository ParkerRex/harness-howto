# Codex Tech Stack (Developer View)

This is a simple, practical breakdown of what Codex is built with and how the
pieces fit together so you can build or extend it.

## At a glance

- **Primary language:** Rust (core runtime, protocol, TUI, app-server)
- **Secondary language:** TypeScript/JavaScript (CLI wrapper, SDKs, MCP tools)
- **Runtime:** Rust binaries + Node.js tooling
- **Storage:** Local filesystem JSONL rollouts under `~/.codex`
- **Tooling:** Cargo, pnpm, Bazel, Nix, `justfile`

## Core runtime (Rust)

**Where:** `codex-rs/`

**What it is:** The agent brain. It owns threads, turns, prompt assembly,
model calls, tool routing, event emission, and persistence.

**Key crates / files:**

- Runtime loop + session state: `codex-rs/core/src/codex.rs`
- Thread/session manager: `codex-rs/core/src/thread_manager.rs`
- Tool router + handlers: `codex-rs/core/src/tools/`
- Protocol types (Op/Event + models): `codex-rs/protocol/`

## Frontends and entry points

**Rust TUI + exec CLI**

- TUI and exec CLI are Rust binaries that talk to codex-core directly.
- They consume `EventMsg` streams from the core runtime and render them.

**Web or other clients**

- The Rust **app-server** exposes a JSON-RPC 2.0 interface over stdio.
- Clients (web UI, other tools) launch the app-server process and stream JSONL
  requests/responses.

Files to read:

- App-server: `codex-rs/app-server/`
- JSON-RPC contract: `codex-rs/app-server/README.md`
- Web client wiring: `agent-runtime-web-db.md`

## Protocols and data model

- **Internal client protocol:** `Op` / `Event` types in `codex-rs/protocol/`.
- **Model exchange:** `ResponseItem` timeline (stored in history and rollouts).
- **App-server:** JSON-RPC 2.0 over stdio (one JSON object per line).

## Tools and integrations

- **Built-in tools** are declared as JSON schema in core, routed via
  `ToolRouter`, and executed by tool handlers.
- **MCP tools** are discovered at runtime via the MCP connection manager and
  routed the same way as built-in tools.
- The shell tool is provided by a separate MCP server in `shell-tool-mcp/`.

Docs worth skimming:

- `subsystems/tools.md`
- `task-ui-and-tool-calls.md`

## Persistence (“DB”)

Codex does not use a database. It records sessions to JSONL rollout files:

- Location: `~/.codex/sessions/YYYY/MM/DD/rollout-*.jsonl`
- Contents: session metadata + `RolloutItem` entries as the turn runs
- Used for: thread list, resume, fork, archive

See:

- `subsystems/persistence.md`
- `agent-runtime-web-db.md`

## SDKs and packaging

- **SDKs:** `sdk/` (e.g., `sdk/typescript/`)
- **CLI wrapper:** `codex-cli/` (TS/JS packaging around Rust binary)

## Build and tooling

- **Rust workspace:** Cargo
- **JS workspace:** pnpm
- **Build systems:** Bazel (`BUILD.bazel`, `MODULE.bazel`), Nix (`flake.nix`)
- **Task runner:** `justfile`

## External dependencies

- **Model APIs** are configured by provider (OpenAI, etc.) in runtime settings.
- **Optional MCP servers** add tools without changing core.

## Where to start in the docs

1. `architecture-overview.md`
2. `reference/directory-map.md`
3. `subsystems/core-runtime.md`
4. `subsystems/tools.md`
