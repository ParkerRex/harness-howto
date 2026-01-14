# Model Selection and Prompting

This subsystem selects the provider handler, builds the system prompt, and
formats tool schemas for provider-specific APIs.

## Provider selection

- Provider selection is driven by `ApiConfiguration` stored in `StateManager` and
  the current mode (plan/act).
- Provider factory: `src/core/api/index.ts` (`buildApiHandler`).
- Provider handlers: `src/core/api/providers/`.

## System prompt assembly

- Prompt registry and components:
  `src/core/prompts/system-prompt/registry/PromptRegistry.ts`.
- Prompt builder: `src/core/prompts/system-prompt/registry/PromptBuilder.ts`.
- Prompt templates and rules: `src/core/prompts/system-prompt/README.md`.
- SystemPromptContext assembled in Task: `src/core/task/index.ts`.

## Tool schema shaping

- Native tool calling is enabled per model via `ClineToolSet.getNativeTools()`.
- Provider-specific tool schema conversions live in
  `src/core/prompts/system-prompt/spec.ts`.
