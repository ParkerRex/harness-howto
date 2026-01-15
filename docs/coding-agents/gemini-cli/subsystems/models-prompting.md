# Model Selection and Prompting

This subsystem selects models, assembles the system prompt, and configures
request routing.

## System prompt assembly

- `getCoreSystemPrompt` builds the default prompt and injects skill summaries.
- Overrides can be provided via `GEMINI_SYSTEM_MD`.
- `GEMINI_WRITE_SYSTEM_MD` can write the built-in prompt to disk.

Key files:

- `packages/core/src/core/prompts.ts`
- `packages/core/src/core/client.ts`
- `packages/core/src/core/geminiChat.ts`

## Model routing

- `ModelRouterService` composes strategies to pick a model.
- Availability and fallback logic live under `availability/` and `fallback/`.

Key files:

- `packages/core/src/routing/modelRouterService.ts`
- `packages/core/src/availability/*`
- `packages/core/src/fallback/handler.ts`
