# OpenCode Architecture Overview

This overview summarizes the client/server runtime and message-part model.

## System overview

Diagram: `diagrams/system-overview.mmd`.

Key components:

- Multiple clients (CLI/SDK/Desktop/Web) talk to a single server API.
- The server owns sessions, message parts, and LLM streaming orchestration.
- Tool registry combines built-ins, plugins, config tools, and MCP sources.
- Session state persists to local JSON storage.
