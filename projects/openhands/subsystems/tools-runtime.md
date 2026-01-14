# Tools and Runtime Actions

OpenHands maps LLM tool calls to Actions and executes them in a runtime sandbox.

## Tool call mapping (legacy V0)

- Tool definitions: `openhands/agenthub/codeact_agent/tools/*`.
- Tool list assembly: `openhands/agenthub/codeact_agent/codeact_agent.py`.
- Tool call parsing -> Actions:
  `openhands/agenthub/codeact_agent/function_calling.py`.
- Action execution -> Observation:
  `openhands/runtime/base.py`, `openhands/runtime/action_execution_server.py`.

## Runtime and sandbox

- Runtime abstraction: `openhands/runtime/base.py`.
- Docker runtime client: `openhands/runtime/impl/docker/docker_runtime.py`.
- Remote and CLI runtimes: `openhands/runtime/impl/remote/`,
  `openhands/runtime/impl/cli/`.
- Action execution server (inside sandbox):
  `openhands/runtime/action_execution_server.py`.

V1 sandbox service (app server side):

- Sandbox models and status: `openhands/app_server/sandbox/sandbox_models.py`.
- Sandbox service interface + polling: `openhands/app_server/sandbox/sandbox_service.py`.
- Implementations: `openhands/app_server/sandbox/`.
