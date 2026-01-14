# OpenCode Repo Map (Top-Level)

At the top level of the `opencode` submodule:

- `packages/opencode/` - core engine (CLI/TUI, server, session/message model,
  tool system, provider + prompt orchestration)
- `packages/plugin/` - plugin API and hooks for tools, auth, prompt transforms
- `packages/sdk/` - OpenAPI schema and generated JS SDK client
- `packages/function/` - Cloudflare Worker + Durable Object for session sharing
- `packages/desktop/` - Tauri desktop wrapper around the UI
- `packages/console/`, `packages/web/` - web apps (console + marketing)
- `specs/` - design notes for APIs and performance
