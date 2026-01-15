# Gemini CLI Architecture Overview

This overview summarizes the CLI/Core split and the tool + policy pipeline.

## System overview

Diagram: `diagrams/system-overview.mmd`.

Key components:

- CLI package handles input, streaming output, and confirmation prompts.
- Core package owns the turn loop, tool scheduling, and policy checks.
- MCP extensions contribute tools, prompts, and resources at runtime.
- Local storage lives under `~/.gemini` with sessions, settings, and memory.
