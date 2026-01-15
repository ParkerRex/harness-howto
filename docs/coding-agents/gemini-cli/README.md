# Gemini CLI Architecture Docs (Manual)

TL;DR: This directory is a manual, per-subsystem breakdown of the Gemini CLI
codebase (the `src/coding-agents/gemini-cli` submodule). It focuses on the CLI/Core split, the
turn loop, tool scheduling, policy gating, prompt/model routing, context
compression, and session persistence. Paths below are relative to the
`src/coding-agents/gemini-cli/` submodule.

## Start here

- Architecture overview: `architecture-overview.md`
- Key patterns: `key-patterns.md`
- Core runtime (CLI/Core, turn loop, message model): `subsystems/core-runtime.md`
- Tools + policy + confirmations: `subsystems/tools.md`
- Model selection + prompting: `subsystems/models-prompting.md`
- Context window + compaction: `subsystems/context-compaction.md`
- Sessions + persistence: `subsystems/persistence-sessions.md`
- Failure modes + safety: `subsystems/safety-failure-modes.md`
- Observability: `subsystems/observability.md`
- Reference map: `reference/directory-map.md`
- Key files: `reference/key-files.md`
- Mental model: `reference/mental-model.md`
- Comparison notes: `comparison.md`
- Changelogs: `CHANGELOG-upstream.md`, `CHANGELOG-docs.md`
