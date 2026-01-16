# Gemini CLI File Tree

Generated from commit `dfb7dc706` on 2026-01-16.

Total tracked files: 1643

## Top-Level Structure

```
gemini-cli/
├── .allstar/                   # Branch protection config
├── .gcp/                       # GCP Docker/release config
├── .gemini/                    # Gemini CLI config, commands, skills
├── .github/                    # GitHub workflows, actions, templates
├── .husky/                     # Git hooks (pre-commit)
├── .vscode/                    # VS Code settings and tasks
├── docs/                       # User and developer documentation
├── evals/                      # Evaluation harness
├── integration-tests/          # End-to-end integration tests
├── packages/                   # Monorepo packages (see below)
├── schemas/                    # JSON schemas for settings/config
├── scripts/                    # Build, lint, release helpers
├── third_party/                # Vendored third-party code
│
├── CONTRIBUTING.md             # Contribution guidelines
├── Dockerfile                  # Container build
├── GEMINI.md                   # Project guidance for Gemini
├── LICENSE                     # Apache 2.0
├── Makefile                    # Build targets
├── README.md                   # Project documentation
├── ROADMAP.md                  # Development roadmap
├── SECURITY.md                 # Security policy
├── esbuild.config.js           # esbuild bundler config
├── eslint.config.js            # ESLint configuration
├── package.json                # Root npm configuration
├── package-lock.json           # Dependency lockfile
└── tsconfig.json               # TypeScript configuration
```

## Packages Directory (`packages/`)

The monorepo contains 5 packages:

```
packages/
├── a2a-server/                 # Agent-to-Agent HTTP server
│   └── src/
│       ├── http/               # HTTP routing and app
│       ├── services/           # A2A service implementations
│       └── ...
│
├── cli/                        # CLI application (Ink/React)
│   └── src/
│       ├── commands/           # CLI command handlers
│       ├── config/             # CLI configuration
│       ├── core/               # CLI core runtime
│       ├── services/           # CLI services
│       ├── ui/                 # Ink UI components
│       ├── utils/              # CLI utilities
│       ├── zed-integration/    # Zed editor integration
│       ├── gemini.tsx          # Main interactive entry
│       ├── nonInteractiveCli.ts # Non-interactive mode
│       └── ...
│
├── core/                       # Shared core runtime
│   └── src/
│       ├── agents/             # Agent implementations
│       ├── availability/       # Service availability checks
│       ├── code_assist/        # Code assistance features
│       ├── commands/           # Core command handling
│       ├── config/             # Configuration management
│       ├── confirmation-bus/   # Confirmation/approval bus
│       ├── core/               # Core runtime (GeminiChat, Turn)
│       ├── fallback/           # Fallback handling
│       ├── hooks/              # Lifecycle hooks
│       ├── ide/                # IDE integration
│       ├── mcp/                # Model Context Protocol
│       ├── output/             # Output formatting
│       ├── policy/             # Policy engine
│       ├── prompts/            # Prompt templates
│       ├── resources/          # Resource management
│       ├── routing/            # Request routing
│       ├── safety/             # Safety checks
│       ├── scheduler/          # Tool scheduler (CoreToolScheduler)
│       ├── services/           # Core services (ChatRecording, etc.)
│       ├── skills/             # Skill system
│       ├── telemetry/          # Telemetry and logging
│       ├── tools/              # Tool implementations
│       └── utils/              # Shared utilities
│
├── test-utils/                 # Shared test utilities
│   └── src/
│       └── ...
│
└── vscode-ide-companion/       # VS Code extension
    └── src/
        ├── extension.ts        # Extension entry point
        └── ...
```

## Documentation (`docs/`)

```
docs/
├── assets/                     # Screenshots and images
├── changelogs/                 # Release changelogs
├── cli/                        # CLI user documentation
│   ├── authentication.md
│   ├── checkpointing.md
│   ├── commands.md
│   ├── custom-commands.md
│   ├── gemini-ignore.md
│   ├── gemini-md.md
│   ├── generation-settings.md
│   ├── headless.md
│   ├── keyboard-shortcuts.md
│   ├── model-routing.md
│   ├── sandbox.md
│   ├── session-management.md
│   ├── settings.md
│   ├── skills.md
│   ├── system-prompt.md
│   ├── telemetry.md
│   ├── themes.md
│   ├── token-caching.md
│   ├── trusted-folders.md
│   └── tutorials/
├── core/                       # Core library documentation
│   ├── memport.md
│   ├── policy-engine.md
│   └── tools-api.md
├── extensions/                 # Extension development docs
├── faq.md                      # Frequently asked questions
├── get-started/                # Getting started guides
└── architecture.md             # Architecture overview
```

## Key Entry Points

| File | Purpose |
|------|---------|
| `packages/cli/src/gemini.tsx` | Main interactive CLI entry (Ink/React) |
| `packages/cli/src/nonInteractiveCli.ts` | Non-interactive CLI mode |
| `packages/a2a-server/src/http/app.ts` | A2A HTTP server entry |
| `packages/vscode-ide-companion/src/extension.ts` | VS Code extension entry |
| `packages/core/src/core/geminiChat.ts` | Core chat client |
| `packages/core/src/scheduler/coreToolScheduler.ts` | Tool scheduling |
| `packages/core/src/services/chatRecordingService.ts` | Session persistence |

## Integration Tests

```
integration-tests/
├── a2a/                        # A2A server tests
├── cli/                        # CLI integration tests
├── docker/                     # Docker-based tests
├── ide/                        # IDE integration tests
├── sandbox/                    # Sandbox tests
└── ...
```

## Schemas

```
schemas/
├── gemini-settings.schema.json # Settings schema
└── ...
```
