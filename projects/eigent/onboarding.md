# Eigent Onboarding

This page is the quick orientation for new engineers. Paths are relative to the
`eigent/` submodule.

## Start here file list (most important)

- `electron/main/init.ts` - backend spawn and port management
- `backend/app/controller/chat_controller.py` - SSE entrypoint
- `backend/app/service/chat_service.py` - task orchestration
- `backend/app/service/task.py` - task lifecycle + actions
- `backend/app/utils/agent.py` - agent setup + tool execution
- `backend/app/utils/workforce.py` - CAMEL workforce orchestration
- `backend/app/utils/listen/toolkit_listen.py` - tool event instrumentation
- `backend/app/utils/toolkit/hybrid_browser_toolkit.py` - browser toolkit
- `backend/app/utils/toolkit/terminal_toolkit.py` - terminal toolkit
- `src/store/chatStore.ts` - UI task state + SSE mapping
- `src/store/projectStore.ts` - project-level session model
- `server/app/controller/chat/step_controller.py` - server sync entrypoint

## Mental model cheat sheet

- Session = project, Task = one user request, SSE drives UI updates.
- Tool events are instrumented at agent and toolkit layers.
- Context is in memory with a hard 100k character limit and no auto-compaction.
- Optional server stores step events for replay/sharing.
