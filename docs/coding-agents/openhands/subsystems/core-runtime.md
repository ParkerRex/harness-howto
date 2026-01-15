# Core Runtime (V0 Agent Loop + V1 App Server)

OpenHands has two overlapping runtimes: the legacy V0 agent loop and the V1 app
server that fronts the external SDK/tools stack.

## Legacy V0 loop

- Websocket server: `server/listen.py` and
  `server/session/session.py`.
- Agent controller: `controller/agent_controller.py`.
- Agent implementation: `agenthub/codeact_agent/codeact_agent.py`.
- Event stream backbone: `events/stream.py`.

Message flow (V0): user input -> EventStream -> AgentController -> LLM tool calls
-> Actions -> Runtime -> Observations -> EventStream -> UI.

## V1 app server

- Entry + routing: `app_server/v1_router.py`.
- Conversation orchestration:
  `app_server/app_conversation/live_status_app_conversation_service.py`.
- Event services: `app_server/event/`.
- Sandbox services: `app_server/sandbox/`.

V1 integrates with external packages (`openhands-sdk`, `openhands-tools`, and
`openhands-agent-server`) to run the agent and tools.
