# Core Runtime (Electron + Backend + SSE)

Eigent runs as a desktop Electron app that launches a local FastAPI runtime and
streams step events to the UI over SSE.

## Boot sequence

- Electron finds a port and spawns uvicorn:
  `electron/main/init.ts`.
- Backend loads environment and wires services:
  `backend/app/component/environment.py`.
- Chat controller handles SSE streaming:
  `backend/app/controller/chat_controller.py`.

## Sessions, threads, tasks

- Session = project (`project_id`), long-lived.
- Task = one run of a user request (`task_id`).
- UI thread is a chat store per project.

Key files:

- `src/store/projectStore.ts`
- `src/store/chatStore.ts`
- `backend/app/service/task.py`
