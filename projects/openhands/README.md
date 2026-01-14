# OpenHands Architecture Docs (Manual)

TL;DR: This directory is a manual, per-subsystem breakdown of the OpenHands
codebase (the `openhands` submodule). It covers the legacy V0 agent loop and the
V1 app server that fronts external SDK/tools, plus runtimes, events, and
persistence. Paths below are relative to the `openhands/` submodule.

## Start here

- Architecture overview: `architecture-overview.md`
- Key patterns: `key-patterns.md`
- Core runtime (V0 loop + V1 app server): `subsystems/core-runtime.md`
- Tools and runtime actions: `subsystems/tools-runtime.md`
- Model selection + prompting: `subsystems/models-prompting.md`
- Context window + compaction: `subsystems/context-compaction.md`
- Sessions + persistence: `subsystems/persistence-sessions.md`
- Safety + failure modes: `subsystems/safety-failure-modes.md`
- Observability: `subsystems/observability.md`
- Reference map: `reference/directory-map.md`
- Key files: `reference/key-files.md`
- Mental model: `reference/mental-model.md`
- Comparison notes: `comparison.md`
- Changelogs: `CHANGELOG-upstream.md`, `CHANGELOG-docs.md`
