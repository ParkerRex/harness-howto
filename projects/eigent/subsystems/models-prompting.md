# Model Selection and Prompting

Eigent selects models based on UI configuration and backend ModelFactory.

## Model selection flow

- UI picks `modelType` in `src/store/authStore.ts`.
- For cloud, platform is inferred from model name (gpt/claude/gemini).
- Backend creates models via `ModelFactory.create(...)` in
  `backend/app/utils/agent.py`.

Key files:

- `src/store/authStore.ts`
- `backend/app/utils/agent.py`
- `backend/app/utils/workforce.py`
