# Cross-Project Comparison (Snapshot)

This snapshot is intentionally high level; see each project's `comparison.md` for
details and caveats.

| Project          | Primary surface     | Runtime shape                              | Tool gating                           | Persistence                             |
| ---------------- | ------------------- | ------------------------------------------ | ------------------------------------- | --------------------------------------- |
| Claude Code Open | CLI/TUI             | Conversation loop + permission hooks       | Permission manager + sandbox          | `~/.claude` sessions, summaries, memory |
| Cline            | VS Code webview     | Extension-host Controller/Task loop        | Approval prompts                      | `~/.cline` task data + settings         |
| Codex            | TUI/CLI/SDK         | Rust core Thread/Session/Turn + Ops/Events | ToolRouter + exec policy              | `~/.codex` JSONL sessions               |
| Eigent           | Electron desktop    | FastAPI backend + SSE                      | Backend toolkits                      | Optional server DB + local state        |
| Gemini CLI       | CLI (Ink)           | Core turn loop + policy engine             | PolicyEngine + confirmation bus       | `~/.gemini` sessions/settings/memory    |
| OpenCode         | CLI/SDK/Desktop/Web | Server API + session/message parts         | Registry merges built-ins/plugins/MCP | Local JSON storage                      |
| OpenHands        | CLI/Web/IDE         | V1 app server + legacy V0 loop             | Runtime action server                 | Event store + file store                |
