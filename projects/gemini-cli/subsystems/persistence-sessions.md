# Sessions and Persistence

This subsystem covers session IDs, local storage, summaries, and checkpointing.

## Session IDs and storage

- Session IDs are generated via `randomUUID` in
  `gemini-cli/packages/core/src/utils/session.ts`.
- Session JSON is stored under `~/.gemini/tmp/<project_hash>/chats/`.
- `ChatRecordingService` records messages, tool calls, thoughts, and tokens.

Key files:

- `gemini-cli/packages/core/src/services/chatRecordingService.ts`
- `gemini-cli/packages/core/src/utils/session.ts`
- `gemini-cli/packages/cli/src/utils/sessions.ts`
- `gemini-cli/packages/cli/src/utils/sessionUtils.ts`

## Session summaries

- Summaries are generated with `SessionSummaryService` and helpers.

Files:

- `gemini-cli/packages/core/src/services/sessionSummaryService.ts`
- `gemini-cli/packages/core/src/services/sessionSummaryUtils.ts`

## Checkpointing

- Optional checkpointing uses Git snapshots + checkpoint JSON stored under the
  project temp directory.

File:

- `gemini-cli/packages/core/src/utils/checkpointUtils.ts`
