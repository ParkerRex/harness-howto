# OpenHands Directory Map

At the top level of the `src/coding-agents/openhands` submodule:

- `app_server/` - V1 FastAPI app server (conversations, sandboxes, events, user/JWT).
- `server/` - Legacy V0 websocket server and sessions.
- `agenthub/` - Legacy agent implementations and prompts.
- `controller/` - Agent controller, state, stuck detection.
- `memory/` - Condenser framework and conversation memory.
- `runtime/` - Runtime abstraction and docker/local/remote runtimes.
- `events/` - Event types, stream, persistence.
- `integrations/` - Git provider integrations.
- `frontend/` - React UI.
- `enterprise/` - Source-available enterprise extensions.
