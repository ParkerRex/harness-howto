# Context Window and Compaction

Context is stored in memory per project and capped by a character limit.

- Conversation history lives in `TaskLock.conversation_history`.
- Hard limit is 100,000 chars; exceeding it emits `context_too_long` SSE and
  disables input.
- There is no automatic compaction; only task summaries are generated for UI.

Key files:

- `backend/app/service/chat_service.py`
- `backend/app/service/task.py`
- `backend/app/model/chat.py`
- `src/components/ChatBox/index.tsx`
