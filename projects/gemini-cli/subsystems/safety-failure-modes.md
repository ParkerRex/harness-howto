# Safety and Failure Modes

This subsystem covers policy enforcement, sandboxing, and common failure cases.

## Policy and confirmations

- `PolicyEngine` enforces approvals and hook execution decisions.
- `MessageBus` coordinates confirmation prompts in the UI.

Key files:

- `gemini-cli/packages/core/src/policy/policy-engine.ts`
- `gemini-cli/packages/core/src/confirmation-bus/message-bus.ts`

## Sandboxing

- Optional sandboxing via `--sandbox` or `GEMINI_SANDBOX`.
- Seatbelt on macOS or container runtimes (Docker/Podman).

Key files:

- `gemini-cli/packages/cli/src/config/sandboxConfig.ts`
- `gemini-cli/packages/cli/src/utils/sandbox.ts`

## Common failure modes

- Invalid stream: `InvalidStreamError` triggers retries
  (`gemini-cli/packages/core/src/core/geminiChat.ts`).
- Loop detection: `LoopDetectionService` emits `GeminiEventType.LoopDetected`
  (`gemini-cli/packages/core/src/services/loopDetectionService.ts`).
- Policy denial: `PolicyEngine` can block tools or require confirmation.
- Tool errors: `ToolExecutor` converts failures into structured responses
  (`gemini-cli/packages/core/src/scheduler/tool-executor.ts`).
- Context overflow: `GeminiClient` emits `ContextWindowWillOverflow` and triggers
  compression (`gemini-cli/packages/core/src/core/client.ts`).
- Quota/availability: fallback logic can switch models or stop execution
  (`gemini-cli/packages/core/src/fallback/handler.ts`).
