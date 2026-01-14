# OpenHands Architecture Overview

This overview summarizes the split between the legacy V0 loop and the V1 app
server plus runtime services.

## System overview

Diagram: `diagrams/system-overview.mmd`.

Key components:

- Clients can connect to both the V1 app server and the legacy V0 websocket server.
- V1 app server fronts external SDK/tools and runtime services.
- V0 core owns the older controller, memory, and event stream.
- Runtime actions are executed via an action execution server and sandbox.
