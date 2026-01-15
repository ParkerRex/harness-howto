# Gemini CLI Mental Model

Paths are relative to the `src/coding-agents/gemini-cli/` submodule.

## Cheat sheet

- Gemini CLI is a local terminal app with a Core runtime that owns model calls,
  tools, and persistence.
- `GeminiClient` orchestrates turns, routing, compression, and event streaming.
- `GeminiChat` is the API adapter that applies system prompt and streams responses.
- Tool calls are scheduled in `CoreToolScheduler`, executed in `ToolExecutor`,
  and governed by `PolicyEngine` + message bus.
- Sessions are JSON files under `~/.gemini/tmp/<project_hash>/chats/` and can be
  resumed or summarized.
- Extensions and MCP servers add tools, prompts, and resources without changing
  Core code.
