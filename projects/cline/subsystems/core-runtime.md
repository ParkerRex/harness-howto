# Core Runtime (Controller, Task, Messages)

This subsystem covers the controller-driven task loop and the message model that
bridges UI messages and provider messages.

## Core components

- Controller: `src/core/controller/index.ts` orchestrates task lifecycle and UI
  updates.
- Task: `src/core/task/index.ts` owns prompt assembly, streaming, tool execution,
  compaction, and persistence.
- MessageStateHandler: `src/core/task/message-state.ts` persists UI and provider
  messages and keeps conversation indices aligned.
- Task state: `src/core/task/TaskState.ts` (and `TaskState` in task files) tracks
  streaming status, assistant content, tool use mapping, and truncation ranges.

## Message model split

Cline tracks two parallel message streams:

- UI messages for the webview: `ClineMessage` in `src/shared/ExtensionMessage.ts`.
- Provider-facing messages for LLM APIs: `ClineStorageMessage` in
  `src/shared/messages/content.ts`.

History metadata (cost, token counts, deletion ranges) is stored in
`src/shared/HistoryItem.ts` and persisted by `StateManager`.

## Message lifecycle (high level)

1) Webview sends user input to the controller.
2) Task initializes state and checkpointing.
3) Mentions and slash commands expand context.
4) Prompt registry assembles the system prompt.
5) ContextManager truncates and prepares history.
6) Provider API streams tokens and tool calls.
7) ToolExecutor executes tools with approvals.
8) MessageStateHandler persists UI + provider history.

Key files:

- `src/core/webview/WebviewProvider.ts`
- `src/core/controller/index.ts`
- `src/core/task/index.ts`
- `src/core/task/StreamResponseHandler.ts`
- `src/core/task/message-state.ts`
