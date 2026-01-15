# Eigent Architecture Overview

This overview summarizes the Electron desktop shell, FastAPI backend, and SSE
streaming model.

## System overview

Diagram: `diagrams/system-overview.mmd`.

Key components:

- Electron main spawns a local FastAPI backend and brokers OS integration.
- The renderer (React/Zustand) consumes SSE step updates from the backend.
- Optional server persistence and sharing live in `server/` with a SQL store.
- Toolkits live in the backend and include a hybrid browser toolkit.
