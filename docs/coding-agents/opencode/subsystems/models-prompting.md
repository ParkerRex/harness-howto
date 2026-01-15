# Model Selection and Prompting

This subsystem chooses models, assembles system prompts, and runs the LLM stream
loop with tools.

## Model selection priority

1) Explicit `input.model` passed to `SessionPrompt.prompt`
2) Agent-specific model config
3) Last user model in session
4) Provider default model

Where it is defined:

- `packages/opencode/src/session/prompt.ts` (createUserMessage + lastModel)
- `packages/opencode/src/agent/agent.ts` (agent defaults + overrides)
- `packages/opencode/src/provider/provider.ts` (defaultModel, provider list)
- `packages/opencode/src/config/config.ts` (user config + overrides)

## System prompt composition

- Provider-specific prompt selection: `packages/opencode/src/session/system.ts`
- Prompt text files: `packages/opencode/src/session/prompt/*.txt`
- Local rule files auto-loaded (AGENTS.md, CLAUDE.md, etc)
- Config `instructions` can include files or URLs
- Plugins can mutate system prompt via
  `experimental.chat.system.transform`

## Stream execution

- `packages/opencode/src/session/llm.ts`
- Merges provider defaults + model options + agent options + variant
- Uses `ai` SDK `streamText` with tool definitions
- Applies `extractReasoningMiddleware` to track reasoning parts
