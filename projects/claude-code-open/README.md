# Claude Code Open Architecture Docs (Manual)

TL;DR: This directory is a manual, per-subsystem breakdown of Claude Code Open
(the `claude-code-open` submodule). It covers the CLI/TUI loop, tool execution
with permissions and sandboxing, prompt assembly, compaction, and on-disk session
storage. Paths below are relative to the `claude-code-open/` submodule.

## Start here

- Architecture overview: `architecture-overview.md`
- Key patterns: `key-patterns.md`
- Core runtime (loop + sessions): `subsystems/core-runtime.md`
- Tools + permissions + sandbox: `subsystems/tools.md`
- Model selection + prompting: `subsystems/models-prompting.md`
- Context window + compaction: `subsystems/context-compaction.md`
- Persistence + memory: `subsystems/persistence-memory.md`
- Safety + failure modes: `subsystems/safety-failure-modes.md`
- Observability: `subsystems/observability.md`
- Reference map: `reference/directory-map.md`
- Key files: `reference/key-files.md`
- Mental model: `reference/mental-model.md`
- Comparison notes: `comparison.md`
- Changelogs: `CHANGELOG-upstream.md`, `CHANGELOG-docs.md`
