# Tools and Toolkits

Eigent instruments tool calls at both the agent and toolkit layers so that the
UI can attach tool output to subtasks.

## Instrumentation layers

1) Agent-level tool execution emits activate/deactivate events.
   - `backend/app/utils/agent.py` (`ListenChatAgent._execute_tool`).

2) Toolkit-level decorator emits events and binds `process_task_id`.
   - `backend/app/utils/listen/toolkit_listen.py`.

UI resolves tool events to subtasks using:

- `resolveProcessTaskIdForToolkitEvent` in `src/store/chatStore.ts`.

## Adding a new tool (summary)

1) Implement toolkit in `backend/app/utils/toolkit/`.
2) Decorate methods with `listen_toolkit` or `auto_listen_toolkit`.
3) Register in `get_toolkits()` in `backend/app/utils/agent.py`.
4) Expose in UI tool picker if needed:
   `src/components/AddWorker/ToolSelect.tsx`.
