# 📷 Open Source Snapshot

I'm obsessed with open source—I think it's the greatest resource for learning how software actually gets built. This repo is my way of keeping tabs on my favorite projects so I can study and learn from them.

Starting with agentic coding tools, but planning to expand to other categories over time.

## How it works

Each project gets a **snapshot**: submodules pointing to the upstream repos, plus architecture breakdowns and documentation I maintain.

- Submodules point to upstream repos
- GitHub Action runs every 3 days to pull latest changes
- If a submodule has 5+ new commits, Claude Code incrementally updates its manual docs under `projects/<submodule>/` using [.prompts/doc-gen-manual.md](.prompts/doc-gen-manual.md)
- Docs commit directly to main

Manual trigger: Actions → "Update submodules & docs" → Run workflow

Open to suggestions on how to make this more useful—ideas and PRs welcome.

## Projects

Stars are from GitHub as of 2026-01-14.

| Project | Category | Writeups | Stars |
| --- | --- | --- | --- |
| [Gemini CLI](https://github.com/google-gemini/gemini-cli) | Agentic Coding | [projects/gemini-cli/README.md](projects/gemini-cli/README.md) | 90.9k |
| [Eigent](https://github.com/eigent-ai/eigent) | Agentic Coding | [projects/eigent/README.md](projects/eigent/README.md) | 4.4k |
| [OpenCode](https://github.com/anomalyco/opencode) | Agentic Coding | [projects/opencode/README.md](projects/opencode/README.md) | 68.6k |
| [Codex](https://github.com/openai/codex) | Agentic Coding | [codex-dotfile-setup.md](codex-dotfile-setup.md)<br>[projects/codex/README.md](projects/codex/README.md) | 56.1k |
| [Cline](https://github.com/cline/cline) | Agentic Coding | [projects/cline/README.md](projects/cline/README.md) | 56.9k |
| [OpenHands](https://github.com/OpenHands/OpenHands) | Agentic Coding | [projects/openhands/README.md](projects/openhands/README.md) | 66.6k |
| [Claude Code Open](https://github.com/kill136/claude-code-open) | Agentic Coding | [projects/claude-code-open/README.md](projects/claude-code-open/README.md) | 66 |

## Docs in this repo

- `comparison/summary.md` for a cross-project snapshot
- Each project under `projects/<name>/` includes:
  - `README.md` (TL;DR + entry points)
  - `architecture-overview.md`
  - `key-patterns.md` + `comparison.md`
  - `subsystems/` and `reference/`
  - `CHANGELOG-upstream.md` + `CHANGELOG-docs.md`
  - `diagrams/*.mmd`
- Additional docs:
  - `claude-dotfile-setup.md`
  - `codex-dotfile-setup.md`
  - `inspect-background-agent-architecture.md`
