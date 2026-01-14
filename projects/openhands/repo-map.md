# OpenHands Repo Map (Top-Level)

At the top level of the `openhands` submodule:

- `openhands/app_server/` - V1 FastAPI app server (conversations, sandboxes, events, user/JWT).
- `openhands/server/` - Legacy V0 websocket server and sessions.
- `openhands/agenthub/` - Legacy agent implementations and prompts.
- `openhands/controller/` - Agent controller, state, stuck detection.
- `openhands/memory/` - Condenser framework and conversation memory.
- `openhands/runtime/` - Runtime abstraction and docker/local/remote runtimes.
- `openhands/events/` - Event types, stream, persistence.
- `openhands/integrations/` - Git provider integrations.
- `frontend/` - React UI.
- `enterprise/` - Source-available enterprise extensions.
