# Persistence and Sharing

This subsystem covers local storage, session summaries, and remote sharing/sync.

## Local storage

- Storage is local JSON under `Global.Path.data/storage`.
- Session and message data are persisted by the session layer.

Key files:

- `packages/opencode/src/storage/storage.ts`
- `packages/opencode/src/global/index.ts`
- `packages/opencode/src/session/index.ts`

## Session summary + diff tracking

- Each assistant step captures a snapshot + patch.
- `SessionSummary` computes diffs and per-message titles.

Files:

- `packages/opencode/src/session/summary.ts`
- `packages/opencode/src/snapshot/index.ts`

## Sharing + sync

- Sharing uses a remote sync backend (Cloudflare Durable Object).
- Local share orchestration in `packages/opencode/src/share/*`.
- Backend API in `packages/function/src/api.ts`.

This supports session sharing and live updates across clients.
