# Gemini CLI Onboarding

This page is the quick orientation for new engineers. Paths are relative to the
`gemini-cli/` submodule.

## Start here file list (most important)

- `gemini-cli/packages/cli/src/gemini.tsx` — CLI entrypoint and interactive startup
- `gemini-cli/packages/cli/src/ui/AppContainer.tsx` — main UI container wiring
- `gemini-cli/packages/cli/src/ui/hooks/useGeminiStream.ts` — streaming loop
- `gemini-cli/packages/core/src/config/config.ts` — central config + registries
- `gemini-cli/packages/core/src/core/client.ts` — turn orchestration, routing
- `gemini-cli/packages/core/src/core/geminiChat.ts` — API streaming, retries
- `gemini-cli/packages/core/src/core/turn.ts` — event model + tool extraction
- `gemini-cli/packages/core/src/core/coreToolScheduler.ts` — tool lifecycle
- `gemini-cli/packages/core/src/scheduler/tool-executor.ts` — tool execution
- `gemini-cli/packages/core/src/policy/policy-engine.ts` — approval rules
- `gemini-cli/packages/core/src/services/chatRecordingService.ts` — persistence
- `gemini-cli/packages/core/src/services/chatCompressionService.ts` — compaction
- `gemini-cli/packages/core/src/tools/tool-registry.ts` — tool discovery
- `gemini-cli/packages/core/src/tools/mcp-client-manager.ts` — MCP discovery
- `gemini-cli/packages/a2a-server/src/http/app.ts` — A2A server HTTP surface

## Mental model cheat sheet

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
