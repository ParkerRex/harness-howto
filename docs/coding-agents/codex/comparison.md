# Codex Comparison Notes

Highlights when comparing Codex to other agents in this repo.

- Surface: TUI/CLI plus a TypeScript SDK fronting the same core runtime.
- Core model: Thread/Session/Turn with Ops + Events as the UI contract.
- Tooling: tool router with structured plan tool events for UI rendering.
- Persistence: JSONL session logs under `~/.codex`.

See `../../comparison/summary.md` for a cross-project snapshot.
