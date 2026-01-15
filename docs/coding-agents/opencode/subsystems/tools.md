# Tools (Registry, State, Subagents)

Tools are first-class parts in MessageV2 and are routed via a registry that
combines built-ins, plugins, config-provided tools, and MCP sources.

## Tool call mapping (state machine)

- Tool parts are created as `pending` on `tool-input-start`.
- Updated to `running` on `tool-call`.
- Finalized to `completed` or `error` on `tool-result` / `tool-error`.
- State lives in `MessageV2.ToolPart`, emitted on the Bus for UI updates.

Key files:

- `packages/opencode/src/session/processor.ts` (tool call state machine)
- `packages/opencode/src/session/message-v2.ts` (ToolPart schema)

## Tool state chart

Diagram: `../diagrams/tool-state-chart.mmd`.

## Tool registry and built-ins

- Tool contract: `Tool.define(id, init)` returns schema + execute.
  - `packages/opencode/src/tool/tool.ts`
- Tool registry builds tool list per agent + provider.
  - `packages/opencode/src/tool/registry.ts`
- Built-ins live in `packages/opencode/src/tool/*`

Tools can be injected via:

- Plugins: `packages/plugin/src/index.ts`
- User config directories: `Config.directories()` in
  `packages/opencode/src/config/config.ts`
- MCP: `packages/opencode/src/mcp/*`

## Subagents (task tool)

- Subagents are sessions created with the `task` tool.
- Tool enforces permissions, spawns a child session, streams results back.

Key files:

- `packages/opencode/src/tool/task.ts`
- `packages/opencode/src/agent/agent.ts`
