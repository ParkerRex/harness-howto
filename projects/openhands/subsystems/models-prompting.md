# Model Selection and Prompting

Legacy V0 uses local LLM config and agent mappings, while V1 passes model and
conversation settings into the external SDK.

## Legacy V0

- LLM config + provider settings: `openhands/core/config/llm_config.py`,
  `openhands/llm/llm.py`.
- Agent to LLM mapping: `openhands/llm/llm_registry.py`.
- Model routing (multi-LLM): `openhands/llm/router/base.py`.
- Prompt templates: `openhands/agenthub/codeact_agent/prompts/`.
- Prompt rendering: `openhands/utils/prompt.py`.

System prompts are injected by the agent/controller in
`openhands/controller/agent.py`.

## V1 app server

The V1 app server delegates prompt/tool selection to the external SDK. The
primary integration entry is:

- `openhands/app_server/app_conversation/live_status_app_conversation_service.py`.
