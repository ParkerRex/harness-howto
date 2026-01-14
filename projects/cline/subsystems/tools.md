# Tools and Approvals

Tools are defined as prompt-facing specs and executed by ToolExecutor with
permission gates and validation.

## Tool definitions and registry

- Tool IDs and read-only set: `src/shared/tools.ts`.
- Prompt specs: `src/core/prompts/system-prompt/tools/*.ts`.
- Tool spec registry/init: `src/core/prompts/system-prompt/tools/init.ts`.
- Provider conversions: `src/core/prompts/system-prompt/spec.ts`.
- Tool set assembly: `src/core/prompts/system-prompt/registry/ClineToolSet.ts`.

## Tool execution and lifecycle

- ToolExecutor: `src/core/task/ToolExecutor.ts`.
- Tool coordinator: `src/core/task/tools/ToolExecutorCoordinator.ts`.
- Handlers: `src/core/task/tools/handlers/`.
- Tool validator (clineignore + workspace boundaries):
  `src/core/task/tools/ToolValidator.ts`.

Tool results are normalized and appended to message history via
`ToolResultUtils` (`src/core/task/tools/utils/ToolResultUtils.ts`).

## Tool lifecycle (state diagram)

Diagram: `../diagrams/tools-lifecycle.mmd`.

## Adding a new tool (summary)

1) Add tool ID in `src/shared/tools.ts`.
2) Add prompt spec in `src/core/prompts/system-prompt/tools/` and register it.
3) Implement handler in `src/core/task/tools/handlers/`.
4) Register handler in `ToolExecutor.registerToolHandlers()`.
5) Update UI message schemas if the tool needs custom payloads.
