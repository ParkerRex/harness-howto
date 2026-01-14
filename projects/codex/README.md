# Codex Architecture Docs (Manual)

TL;DR: This directory is a manual, per-subsystem breakdown of the Codex CLI codebase
(the Rust implementation inside the `codex` submodule). It focuses on the agent loop,
model prompting, tool routing, context/compaction, persistence, and safety. Paths
below are relative to the `codex/` submodule.

## Start here

- Architecture overview: `architecture-overview.md`
- Key patterns: `key-patterns.md`
- Core runtime (threads, sessions, turns): `subsystems/core-runtime.md`
- Model selection + prompting: `subsystems/models-prompting.md`
- Tool system + plan tool: `subsystems/tools.md`
- Context window + compaction: `subsystems/context-compaction.md`
- Memory + persistence: `subsystems/persistence.md`
- Safety + failure modes: `subsystems/safety-failure-modes.md`
- Reference map: `reference/directory-map.md`
- Key files: `reference/key-files.md`
- Mental model: `reference/mental-model.md`
- Comparison notes: `comparison.md`
- Changelogs: `CHANGELOG-upstream.md`, `CHANGELOG-docs.md`
