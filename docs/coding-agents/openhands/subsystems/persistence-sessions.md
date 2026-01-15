# Sessions and Persistence

OpenHands persists events and conversation metadata in both the legacy V0 stack
and the V1 app server.

## Legacy V0 sessions

- Websocket session and AgentSession:
  `server/session/session.py`,
  `server/session/agent_session.py`.
- Event stream/store:
  `events/stream.py`, `events/event_store.py`.
- Conversation metadata storage:
  `storage/conversation/`.

## V1 conversations and events

- App conversation models and status:
  `app_server/app_conversation/app_conversation_models.py`.
- Event services and storage:
  `app_server/event/`.
- Sandbox status and startup lifecycle:
  `app_server/sandbox/`.
