# OpenHands Key Patterns

- Two coexisting architectures: legacy V0 loop and V1 app server.
- V1 delegates tool execution to external SDKs and tool servers.
- Runtime actions are executed through a dedicated action server + sandbox.
- Event stores capture both V0 streams and V1 app events.
