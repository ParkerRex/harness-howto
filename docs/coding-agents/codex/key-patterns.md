# Codex Key Patterns

- ResponseItem is the canonical transcript shape for messages, tools, and UI
  events.
- UI/core interaction is modeled as Ops and Events in the protocol layer.
- ModelInfo drives per-model prompt templates, truncation policy, and tool
  support.
- The plan/todos tool emits structured events so UIs can render checklists.
- Compaction can run inline or remotely depending on configuration.
