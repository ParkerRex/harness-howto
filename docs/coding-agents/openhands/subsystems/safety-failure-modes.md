# Safety and Failure Modes

OpenHands handles runtime and provider errors across the legacy V0 loop and the
V1 app server.

## Common failure modes (V0)

- LLM errors, rate limits, and context overflow:
  `controller/agent_controller.py`.
- Tool call validation failures -> ErrorObservation:
  `agenthub/codeact_agent/function_calling.py`.
- Runtime disconnects and sandbox issues:
  `runtime/runtime_status.py`.
- Action execution server validation and file errors:
  `runtime/action_execution_server.py`.

## V1 app server

- Sandbox status and startup errors:
  `app_server/sandbox/sandbox_service.py`.
- Start task lifecycle error states:
  `app_server/app_conversation/app_conversation_models.py`.
