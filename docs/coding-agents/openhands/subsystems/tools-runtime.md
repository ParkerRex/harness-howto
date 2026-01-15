# Tools and Runtime Actions

OpenHands maps LLM tool calls to Actions and executes them in a runtime sandbox.

## Tool call mapping (legacy V0)

- Tool definitions: `agenthub/codeact_agent/tools/*`.
- Tool list assembly: `agenthub/codeact_agent/codeact_agent.py`.
- Tool call parsing -> Actions:
  `agenthub/codeact_agent/function_calling.py`.
- Action execution -> Observation:
  `runtime/base.py`, `runtime/action_execution_server.py`.

## Runtime and sandbox

- Runtime abstraction: `runtime/base.py`.
- Docker runtime client: `runtime/impl/docker/docker_runtime.py`.
- Remote and CLI runtimes: `runtime/impl/remote/`,
  `runtime/impl/cli/`.
- Action execution server (inside sandbox):
  `runtime/action_execution_server.py`.

V1 sandbox service (app server side):

- Sandbox models and status: `app_server/sandbox/sandbox_models.py`.
- Sandbox service interface + polling: `app_server/sandbox/sandbox_service.py`.
- Implementations: `app_server/sandbox/`.
