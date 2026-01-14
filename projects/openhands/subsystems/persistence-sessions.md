# Sessions and Persistence

OpenHands persists events and conversation metadata in both the legacy V0 stack
and the V1 app server.

## Legacy V0 sessions

- Websocket session and AgentSession:
  `openhands/server/session/session.py`,
  `openhands/server/session/agent_session.py`.
- Event stream/store:
  `openhands/events/stream.py`, `openhands/events/event_store.py`.
- Conversation metadata storage:
  `openhands/storage/conversation/`.

## V1 conversations and events

- App conversation models and status:
  `openhands/app_server/app_conversation/app_conversation_models.py`.
- Event services and storage:
  `openhands/app_server/event/`.
- Sandbox status and startup lifecycle:
  `openhands/app_server/sandbox/`.
