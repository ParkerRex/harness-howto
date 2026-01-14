# Persistence and Sharing

Eigent can sync step events to a server for replay and sharing.

## Server persistence

- ChatHistory: `server/app/model/chat/chat_history.py`.
- ChatStep event log: `server/app/model/chat/chat_step.py`.
- ChatSnapshot: `server/app/model/chat/chat_snpshot.py`.
- Share tokens: `server/app/model/chat/chat_share.py`.

## MCP configuration

- MCP config file: `~/.eigent/mcp.json`.
- Config management: `electron/main/utils/mcpConfig.ts`.
- Tool install/uninstall endpoints:
  `backend/app/controller/tool_controller.py`.
