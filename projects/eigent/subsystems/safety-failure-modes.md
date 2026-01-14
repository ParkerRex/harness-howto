# Safety and Failure Modes

Eigent surfaces runtime errors and budget/context failures via SSE events.

Common failure cases:

- SSE timeout after 10 minutes idle:
  `backend/app/controller/chat_controller.py`.
- Budget exceeded -> `budget_not_enough`:
  `backend/app/utils/agent.py`.
- Invalid/empty task during decomposition:
  `backend/app/utils/workforce.py`.
- Tool failures reported from agent execution:
  `backend/app/utils/agent.py`.
- Context too long -> `context_too_long`:
  `backend/app/service/chat_service.py`.
