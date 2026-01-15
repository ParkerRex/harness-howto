# Sessions and Persistence

This subsystem covers session IDs, local storage, summaries, and checkpointing.

## Session IDs and storage

- Session IDs are generated via `randomUUID` in
  `packages/core/src/utils/session.ts`.
- Session JSON is stored under `~/.gemini/tmp/<project_hash>/chats/`.
- `ChatRecordingService` records messages, tool calls, thoughts, and tokens.

Key files:

- `packages/core/src/services/chatRecordingService.ts`
- `packages/core/src/utils/session.ts`
- `packages/cli/src/utils/sessions.ts`
- `packages/cli/src/utils/sessionUtils.ts`

## Session summaries

- Summaries are generated with `SessionSummaryService` and helpers.

Files:

- `packages/core/src/services/sessionSummaryService.ts`
- `packages/core/src/services/sessionSummaryUtils.ts`

## Checkpointing

- Optional checkpointing uses Git snapshots + checkpoint JSON stored under the
  project temp directory.

File:

- `packages/core/src/utils/checkpointUtils.ts`
