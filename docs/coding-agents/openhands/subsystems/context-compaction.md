# Context Window and Compaction

OpenHands uses ConversationMemory and the condenser framework to keep context
under limits.

## Context building

- ConversationMemory builds LLM messages and filters large observations:
  `memory/conversation_memory.py`.
- Max message size and caching are applied during agent steps:
  `agenthub/codeact_agent/codeact_agent.py`.
- Context window errors trigger condensation requests:
  `controller/agent_controller.py`.

## Compaction (condenser)

- Condenser base + registry: `memory/condenser/condenser.py`.
- Condenser implementations: `memory/condenser/impl/*`.
- View object injects summaries and drops forgotten event IDs:
  `memory/view.py`.
- Condensation actions: `events/action/agent.py`.
