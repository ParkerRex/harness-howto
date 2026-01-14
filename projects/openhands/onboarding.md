# OpenHands Onboarding

This page is the quick orientation for new engineers. Paths are relative to the
`openhands/` submodule.

## Start here file list (most important)

- `openhands/agenthub/codeact_agent/codeact_agent.py` - main agent loop
- `openhands/controller/agent_controller.py` - event-driven control flow
- `openhands/events/stream.py` - event stream and persistence
- `openhands/memory/conversation_memory.py` - message assembly
- `openhands/runtime/action_execution_server.py` - sandbox action execution
- `openhands/app_server/app_conversation/live_status_app_conversation_service.py`
- `openhands/app_server/sandbox/sandbox_service.py`

## Mental model cheat sheet

- V0: event stream drives the agent loop; Actions execute in a runtime and yield
  Observations.
- V1: app server orchestrates sandboxes and conversations and delegates agent
  logic to external SDK/tools.
- Condensers keep context under limits by summarizing event history.
