# Gemini CLI Key Files

Paths are relative to the `src/coding-agents/gemini-cli/` submodule.

## Start here file list

- `packages/cli/src/gemini.tsx` — CLI entrypoint and interactive startup
- `packages/cli/src/ui/AppContainer.tsx` — main UI container wiring
- `packages/cli/src/ui/hooks/useGeminiStream.ts` — streaming loop
- `packages/core/src/config/config.ts` — central config + registries
- `packages/core/src/core/client.ts` — turn orchestration, routing
- `packages/core/src/core/geminiChat.ts` — API streaming, retries
- `packages/core/src/core/turn.ts` — event model + tool extraction
- `packages/core/src/core/coreToolScheduler.ts` — tool lifecycle
- `packages/core/src/scheduler/tool-executor.ts` — tool execution
- `packages/core/src/policy/policy-engine.ts` — approval rules
- `packages/core/src/services/chatRecordingService.ts` — persistence
- `packages/core/src/services/chatCompressionService.ts` — compaction
- `packages/core/src/tools/tool-registry.ts` — tool discovery
- `packages/core/src/tools/mcp-client-manager.ts` — MCP discovery
- `packages/a2a-server/src/http/app.ts` — A2A server HTTP surface
