# Gemini CLI Key Patterns

- Clean split between CLI UX and `@google/gemini-cli-core` runtime.
- Tool scheduling passes through a policy engine and confirmation bus.
- Context compression is a dedicated service with hooks around summarization.
- Tool checkpoints and git snapshots are part of the persistence layer.
