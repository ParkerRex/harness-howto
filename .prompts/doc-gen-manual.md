# OSS Codebase Architecture Documentation Generator (Manual Structure)

## Purpose
Update the manual, per-subsystem architecture docs under `projects/<submodule>/`.
These docs are hand-curated and should be edited incrementally rather than
regenerated. Keep structure stable, patch only the parts affected by code
changes, and preserve wording where still accurate.

## Target Output
Update the existing files under `projects/<submodule>/`:

- `README.md` (TL;DR + entry points)
- `architecture-overview.md`
- `key-patterns.md`
- `comparison.md`
- `CHANGELOG-upstream.md`
- `CHANGELOG-docs.md`
- `reference/*.md` (key-files, directory-map, mental-model, project-snapshot)
- `diagrams/*.mmd` (Mermaid diagrams, linked from docs)
- `subsystems/*.md` (core runtime, tools, models/prompting, context/compaction,
  persistence, safety, observability, etc.)

`repo-map.md` and `onboarding.md` are legacy stubs that should link to the
reference docs.

Do NOT create a new top-level `{submodule}-architecture.md` file when a manual
structure exists.

---

## Instructions

For each submodule listed:

1. Read the current docs under `projects/<submodule>/` to understand structure.
2. Use `git log` and `git diff` scoped to the submodule to identify changes since
   the last doc update.
3. Patch only the relevant sections/files. Do not rewrite the full docs.
4. If missing, add `reference/project-snapshot.md` using
   `projects/_template/project-snapshot.md` as the template.
5. Keep diagrams and file paths accurate. Mermaid diagrams live in
   `diagrams/*.mmd` and should be linked from docs instead of inlined.
   Only update diagrams if underlying flows changed.
6. Preserve tone and file organization; add new subsystem files only if the
   current structure is missing a required area.

---

## Focus Areas (prioritized)

1. Sessions & threads
2. Tool call mapping + policy gates
3. Model selection & system prompting
4. Context window management + compaction
5. Persistence & memory
6. Failure modes & safety
7. Observability (if present)
8. Clients & surfaces (if present)
9. Tech stack, repo shape, data flow, tests, error handling, sharp edges (if present)

---

## Output Constraints

- Update only files inside `projects/<submodule>/`.
- Use minimal diffs. No large rewrites.
- Prefer patching existing bullets and diagrams to preserve layout.
