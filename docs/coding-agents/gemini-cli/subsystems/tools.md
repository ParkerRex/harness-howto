# Tools, Policy, and Confirmations

Tools are defined in Core, scheduled through a tool scheduler, and gated by a
policy engine + confirmation bus.

## Tool call mapping (state machine)

Tool call lifecycle is modeled in `packages/core/src/scheduler/types.ts`
and driven by `CoreToolScheduler`.

Diagram: `../diagrams/tools-state-machine.mmd`.

Key components:

- Tool definitions: `packages/core/src/tools/tools.ts`.
- Registry: `packages/core/src/tools/tool-registry.ts`.
- Scheduler: `packages/core/src/core/coreToolScheduler.ts`.
- Executor: `packages/core/src/scheduler/tool-executor.ts`.
- Policy engine: `packages/core/src/policy/policy-engine.ts`.
- Confirmation bus: `packages/core/src/confirmation-bus/message-bus.ts`.

## How to build a new tool (summary)

1) Implement a tool by extending `BaseDeclarativeTool` and `BaseToolInvocation`.
2) Define a `FunctionDeclaration` schema for model calls.
3) Register the tool in `Config.createToolRegistry`.
4) Implement confirmation details or policy metadata.
5) Add tests in `packages/core/src/tools/*.test.ts`.
