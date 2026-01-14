# Codex Key Files

Paths are relative to the `codex/` submodule.

## Start here file list

- `codex-rs/core/src/codex.rs` - main agent loop, session/turn orchestration
- `codex-rs/core/src/thread_manager.rs` - thread creation + session wiring
- `codex-rs/core/src/context_manager/history.rs` - transcript + token accounting
- `codex-rs/core/src/tools/router.rs` - tool call mapping
- `codex-rs/core/src/tools/spec.rs` - tool definitions + registration
- `codex-rs/core/src/models_manager/model_info.rs` - model prompts and behavior
- `codex-rs/core/src/project_doc.rs` - AGENTS.md discovery + user instructions
- `codex-rs/core/src/compact.rs` - summary-based compaction
- `codex-rs/core/src/rollout/recorder.rs` - persistent session logs
- `codex-rs/protocol/src/protocol.rs` - public ops/events contract
