# Context Window and Compaction

Cline tracks context window size per model, trims history, and supports manual
and automatic compaction flows.

## Context window management

- Context window info: `src/core/context/context-management/context-window-utils.ts`.
- ContextManager: `src/core/context/context-management/ContextManager.ts`.
- Context optimizations (dedupe file reads, truncation):
  `ContextManager.applyContextOptimizations`.
- Truncation metadata is persisted alongside task history via
  `src/core/task/message-state.ts`.

## Compaction

- Manual compaction: `condense` tool (`src/core/task/tools/handlers/CondenseHandler.ts`)
  and slash commands `/smol` or `/compact` (`src/core/prompts/commands.ts`).
- Automatic compaction: `summarize_task` tool + summarize prompt
  (`src/core/prompts/contextManagement.ts`,
  `src/core/task/tools/handlers/SummarizeTaskHandler.ts`).
- Pre-compact hooks: `src/core/hooks/precompact-executor.ts`.
- Continuation prompt for rehydration: `continuationPrompt` in
  `src/core/prompts/contextManagement.ts`.
