# Safety and Failure Modes

Cline gates tool execution with approvals and validations, and surfaces common
failure cases through UI prompts and telemetry.

## Safety and approvals

- Command approvals and permission gating:
  `src/core/permissions/CommandPermissionController.ts`.
- Tool validation and clineignore rules:
  `src/core/task/tools/ToolValidator.ts`.
- Hook cancellations: `src/core/hooks/*`.

## Common failure cases

- Context overflow: `src/core/context/context-management/context-error-handling.ts`.
- Provider errors: `src/services/error/*` and `src/core/task/index.ts`.
- Tool failures: `ToolExecutor.handleError()` in `src/core/task/ToolExecutor.ts`.
- Browser failures with fallback to local:
  `src/services/browser/BrowserSession.ts`.
- Checkpoint errors surfaced in task state:
  `src/integrations/checkpoints/factory.ts`.
