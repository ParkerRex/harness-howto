# OpenHands Mental Model

Paths are relative to the `openhands/` submodule.

## Cheat sheet

- V0: event stream drives the agent loop; Actions execute in a runtime and yield
  Observations.
- V1: app server orchestrates sandboxes and conversations and delegates agent
  logic to external SDK/tools.
- Condensers keep context under limits by summarizing event history.
