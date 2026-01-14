# Core Runtime (CLI/Core, Turns, Data Model)

This subsystem covers the CLI/Core split, the turn loop, and the core data model
used for session recording.

## Execution environment (CLI + Core)

- CLI entrypoint: `gemini-cli/packages/cli/src/gemini.tsx`.
- Non-interactive mode: `gemini-cli/packages/cli/src/nonInteractiveCli.ts`.
- Core config wiring: `gemini-cli/packages/core/src/config/config.ts`.

Gemini CLI runs locally as a Node.js process. Optional sandboxing is available
via `--sandbox` or `GEMINI_SANDBOX` (Seatbelt on macOS or containers).

- Sandbox config: `gemini-cli/packages/cli/src/config/sandboxConfig.ts`.
- Sandbox startup: `gemini-cli/packages/cli/src/utils/sandbox.ts`.
- Shell execution: `gemini-cli/packages/core/src/services/shellExecutionService.ts`.

## Core data model

Session records are defined in `gemini-cli/packages/core/src/services/chatRecordingService.ts`:

- `ConversationRecord` holds session metadata and message list.
- `MessageRecord` stores message parts, tool calls, token usage, and model.
- `ToolCallRecord` records tool name, args, result, and status.

Related tool runtime types live in `gemini-cli/packages/core/src/scheduler/types.ts`.

## Message lifecycle (high level)

1) CLI input (interactive or non-interactive).
2) Core config wires registries, policy engine, hooks, and client.
3) `GeminiClient.sendMessageStream` starts a turn.
4) `GeminiChat.sendMessageStream` streams model responses and retries.
5) `Turn.run` converts function calls into tool call requests.
6) `CoreToolScheduler` validates/executes tool calls.
7) Tool responses are appended and the model stream continues.
8) `ChatRecordingService` persists session JSON; telemetry emits events.

Key files:

- `gemini-cli/packages/core/src/core/client.ts`
- `gemini-cli/packages/core/src/core/geminiChat.ts`
- `gemini-cli/packages/core/src/core/turn.ts`
- `gemini-cli/packages/core/src/core/coreToolScheduler.ts`
- `gemini-cli/packages/core/src/services/chatRecordingService.ts`
