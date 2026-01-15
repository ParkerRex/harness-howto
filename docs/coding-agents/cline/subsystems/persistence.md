# Persistence and History

Cline persists task history to disk and keeps a separate UI message log and
provider history log per task.

## Task storage

Task history is stored under `~/.cline/data/tasks/<taskId>/`:

- `api_conversation_history.json`
- `ui_messages.json`
- `context_history.json`

Persistence is handled by `MessageStateHandler`:

- `src/core/task/message-state.ts`
- `src/core/storage/disk.ts`

## Global history

Global task history and settings are stored by `StateManager` and keyed by
`HistoryItem` entries:

- `src/core/storage/StateManager.ts`
- `src/shared/HistoryItem.ts`
- `src/shared/storage/state-keys.ts`
