# OpenCode Mental Model

Paths are relative to the `opencode/` submodule.

## Practical onboarding path

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

## Cheat sheet

- A session is a thread. A message is a user/assistant turn. A part is a typed
  chunk of content (text, tool, file, etc).
- The prompt loop builds the system prompt + message history, resolves tools,
  then streams the model.
- Tool calls are first-class parts with state; UI watches those parts.
- Agents are configurations: permissions + prompts + model choices.
- Compaction happens when context is full: it summarizes and continues.
