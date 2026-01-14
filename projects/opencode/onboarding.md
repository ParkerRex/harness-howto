# OpenCode Onboarding

This page is the quick orientation for new engineers. Paths are relative to the
`opencode/` submodule.

## Start here file list (most important)

- `packages/opencode/src/index.ts` - entry + CLI commands
- `packages/opencode/src/server/server.ts` - HTTP API surface
- `packages/opencode/src/session/index.ts` - sessions (create, fork, children)
- `packages/opencode/src/session/message-v2.ts` - message parts model
- `packages/opencode/src/session/prompt.ts` - prompt orchestration loop
- `packages/opencode/src/session/processor.ts` - stream event handling
- `packages/opencode/src/session/llm.ts` - LLM stream execution
- `packages/opencode/src/session/system.ts` - system prompt selection
- `packages/opencode/src/tool/tool.ts` - tool definition contract
- `packages/opencode/src/tool/registry.ts` - tool registry
- `packages/plugin/src/index.ts` - plugin hooks
- `packages/sdk/openapi.json` - OpenAPI schema for SDK

## Practical onboarding path (fast ramp)

1) Core architecture: `packages/opencode/src/index.ts`
2) Sessions + messages:
   - `packages/opencode/src/session/index.ts`
   - `packages/opencode/src/session/message-v2.ts`
   - `packages/opencode/src/session/prompt.ts`
   - `packages/opencode/src/session/processor.ts`
3) Model + prompts:
   - `packages/opencode/src/session/llm.ts`
   - `packages/opencode/src/session/system.ts`
   - `packages/opencode/src/session/prompt/*.txt`
4) Tools:
   - `packages/opencode/src/tool/tool.ts`
   - `packages/opencode/src/tool/registry.ts`
   - `packages/opencode/src/tool/*`
5) Extensions + SDK:
   - `packages/plugin/src/index.ts`
   - `packages/sdk/openapi.json`
   - `packages/sdk/js/src/*`
6) Context + compaction:
   - `packages/opencode/src/session/compaction.ts`
   - `packages/opencode/src/session/summary.ts`

## Mental model cheat sheet

- A session is a thread. A message is a user/assistant turn. A part is a typed
  chunk of content (text, tool, file, etc).
- The prompt loop builds the system prompt + message history, resolves tools,
  then streams the model.
- Tool calls are first-class parts with state; UI watches those parts.
- Agents are configurations: permissions + prompts + model choices.
- Compaction happens when context is full: it summarizes and continues.
