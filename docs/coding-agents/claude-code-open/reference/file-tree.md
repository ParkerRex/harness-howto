# Claude Code Open File Tree

Generated from commit `e1ed119` on 2026-01-16.

Total tracked files: 740

## Top-Level Structure

```
claude-code-open/
├── .claude/                    # Claude-specific settings and tool permissions
├── .github/                    # GitHub workflows and funding
├── .lh/                        # LH tooling metadata
├── .vscode/                    # VS Code launch configuration
├── scripts/                    # Development and testing utilities
├── src/                        # Main source code (see below)
├── tests/                      # Test suites (unit, integration, e2e)
├── CLAUDE.md                   # Project guidance for Claude Code
├── README.md                   # Project documentation (English)
├── README.zh-CN.md             # Project documentation (Chinese)
├── package.json                # npm configuration (v2.1.4)
├── package-lock.json           # Dependency lockfile
├── tsconfig.json               # TypeScript configuration
├── tsconfig.test.json          # Test-specific TypeScript config
├── vitest.config.ts            # Vitest test runner config
└── vitest.minimal.config.ts    # Minimal test configuration
```

## Source Directory (`src/`)

```
src/
├── cli.ts                      # CLI entry point (Commander.js)
├── index.ts                    # Main exports barrel
├── version.ts                  # Version info
├── web-cli.ts                  # Web UI entry point (Express + WebSocket)
│
├── agents/                     # Agent implementations (explore, plan, monitor, etc.)
├── auth/                       # Authentication (keychain, OAuth, settings)
├── background/                 # Background task execution and shell management
├── blueprint/                  # Blueprint/task-tree orchestration system
├── checkpoint/                 # Checkpoint/snapshot management
├── chrome/                     # Chrome integration
├── chrome-mcp/                 # Chrome MCP server implementation
├── codesign/                   # Code signing utilities
├── commands/                   # CLI command handlers
├── config/                     # Configuration loading and management
├── context/                    # Context window management and compaction
├── core/                       # Core runtime (client, session, loop)
├── diagnostics/                # Diagnostic utilities
├── env/                        # Environment detection and setup
├── examples/                   # Example files
├── git/                        # Git integration utilities
├── github/                     # GitHub API integration
├── hooks/                      # Pre/post tool execution hooks
├── ide/                        # IDE integration
├── lifecycle/                  # Application lifecycle management
├── lsp/                        # Language Server Protocol integration
├── map/                        # Codebase mapping utilities
├── mcp/                        # Model Context Protocol (MCP) client/server
├── media/                      # Media handling (images, etc.)
├── memory/                     # Memory/conversation summarization
├── models/                     # Model configuration and selection
├── network/                    # Network utilities and proxying
├── notifications/              # User notification system
├── organization/               # Organization/team features
├── parser/                     # Tree-sitter code parsing
├── permissions/                # Permission management system
├── plan/                       # Plan mode implementation
├── plugins/                    # Plugin system
├── prompt/                     # Prompt assembly and templates
├── providers/                  # Model provider abstraction
├── ratelimit/                  # Rate limiting logic
├── renderer/                   # Terminal rendering utilities
├── rewind/                     # Rewind/undo functionality
├── rules/                      # CLAUDE.md and rule processing
├── sandbox/                    # Sandboxed execution (Bubblewrap)
├── search/                     # Search utilities (ripgrep integration)
├── security/                   # Security utilities
├── session/                    # Session persistence and management
├── streaming/                  # JSON streaming for API responses
├── telemetry/                  # Telemetry and event logging
├── teleport/                   # Teleport/remote features
├── tools/                      # Tool implementations (25+ tools)
├── trust/                      # Trust and verification
├── types/                      # TypeScript type definitions
├── ui/                         # React + Ink terminal UI components
├── updater/                    # Auto-update functionality
├── utils/                      # Shared utilities
└── web/                        # Web UI components and server
```

## Key Entry Points

| File | Purpose |
|------|---------|
| `src/cli.ts` | Main CLI entry, argument parsing with Commander.js |
| `src/web-cli.ts` | Web server mode with Express + WebSocket |
| `src/core/loop.ts` | Main conversation orchestrator |
| `src/core/client.ts` | Anthropic API wrapper with retry logic |
| `src/core/session.ts` | Session state management |
| `src/tools/index.ts` | Tool registry and base tool class |

## Test Structure

```
tests/
├── integration/                # Integration tests
├── e2e/                        # End-to-end CLI tests
│   ├── cli-basic.test.ts
│   ├── cli-session.test.ts
│   ├── cli-tools.test.ts
│   └── run-all.ts
└── (unit tests colocated in src/ as *.test.ts)
```
