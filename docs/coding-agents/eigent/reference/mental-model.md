# Eigent Mental Model

Paths are relative to the `src/coding-agents/eigent/` submodule.

## Cheat sheet

- Session = project, Task = one user request, SSE drives UI updates.
- Tool events are instrumented at agent and toolkit layers.
- Context is in memory with a hard 100k character limit and no auto-compaction.
- Optional server stores step events for replay/sharing.
