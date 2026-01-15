# Project Snapshot (Template)

Use this template as `projects/<submodule>/reference/project-snapshot.md`.
Keep it short, scannable, and grounded in real files/paths.

## 0. Snapshot metadata

- Snapshot UTC:
- Snapshot source ref (commit or tag):
- Snapshot cadence (e.g., every 3 days):
- Snapshot scope (what changed triggers a snapshot):

## 1. Doc set (existing artifacts)

List the docs that already exist in this project folder so readers know where to
go next and what is covered elsewhere.

- `README.md` - TL;DR, entry points, and where to start reading.
- `architecture-overview.md` - System-level summary + primary data/control flows.
- `key-patterns.md` - Repeated architectural patterns, conventions, and invariants.
- `comparison.md` - How this project differs from other agents in the repo.
- `onboarding.md` - Legacy stub that points to reference docs.
- `repo-map.md` - Legacy stub that points to reference docs.
- `CHANGELOG-upstream.md` - Upstream project changes since last sweep.
- `CHANGELOG-docs.md` - Documentation updates made in this repo.
- `reference/directory-map.md` - Top-level folders + what they own.
- `reference/file-tree.md` - Top-level file tree (tracked files, depth-limited).
- `reference/key-files.md` - High-leverage files for core flows.
- `reference/mental-model.md` - Mental model + suggested reading path.
- `reference/project-snapshot.md` - This snapshot (overview + gaps).
- `subsystems/` - Deep dives per area (core runtime, tools, models, context,
  persistence, safety, observability).
- `diagrams/` - Mermaid diagrams for system overview + major flows.

Notes: list any project-specific extras (e.g. extra diagrams, custom subsystems,
or special references) so readers don't miss them.

## 2. Tech stack

- Languages (primary + secondary):
- Runtimes/frameworks:
- Build/package tooling:
- Infra/services (DB, queue, cache, external APIs):
- Observability (logs/metrics/tracing):

## 3. Repo stats (as of YYYY-MM-DD)

- Commits: `git -C <submodule> rev-list --count HEAD`
- Contributors: `git -C <submodule> shortlog -sne | wc -l`
- Default branch:
- License:
- Release cadence (tags or releases):

## 4. Repo shape & entry points (10–15 min)

- Entry points (where execution starts):
- "Happy path" flow (what a typical run does):
- Monorepo or single-purpose:
- Key top-level dirs and why they matter:
- Build/run files (README, package.json, go.mod, Makefile, etc.):

## 5. Repo file tree (top-level)

- `reference/file-tree.md` (tracked files, depth-limited)

## 6. Architecture in one whiteboard diagram

- Diagram: `diagrams/<name>.mmd` (add/update if needed)
- Flow: User/Event → Entry Point → Core Logic → Side Effects
- Boundaries (where data changes shape):
- Stateful vs stateless parts:
- Sync vs async:

## 7. Data flow > code flow

- Where data enters the system:
- Validation → transformation → persistence → response:
- Domain objects vs DTOs:
- Where schemas live:
- Ownership (who "owns" each piece of data):
- Where data mutates:
- What assumptions are baked in:

## 8. Tests tell you what matters

- What has tests vs what doesn't:
- Edge cases covered:
- Mock-heavy zones (signals slowness/instability):
- Snapshot vs behavioral tests:
- What breaks most often (if known):

## 9. Error handling & logging

- Structured logging or ad-hoc logs:
- Centralized error handling:
- Retry logic / timeouts / circuit breakers:
- If this breaks in prod, what do we see?

## 10. Observability & operations (optional)

- Metrics/tracing tooling:
- Feature flags / env config:
- Deployment shape (local, cloud, etc.):

## 11. Sharp edges & footguns

- Global state:
- Hidden side effects:
- Implicit conventions:
- Magic env flags:
- Temporal coupling (this must run before that):
