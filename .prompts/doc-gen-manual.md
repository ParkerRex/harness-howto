# OSS Codebase Architecture Documentation Generator (Manual Structure)

## Purpose
Update the manual, per-subsystem architecture docs under `projects/<submodule>/`.
These docs are hand-curated and should be edited incrementally rather than
regenerated. Keep structure stable, patch only the parts affected by code
changes, and preserve wording where still accurate.

## Target Output
Update the existing files under `projects/<submodule>/`:

- `README.md` (overview + system map)
- `repo-map.md`
- `onboarding.md`
- `subsystems/*.md` (core runtime, tools, models/prompting, context/compaction,
  persistence, safety, observability, etc.)

Do NOT create a new top-level `{submodule}-architecture.md` file when a manual
structure exists.

---

## Instructions

For each submodule listed:

1. Read the current docs under `projects/<submodule>/` to understand structure.
2. Use `git log` and `git diff` scoped to the submodule to identify changes since
   the last doc update.
3. Patch only the relevant sections/files. Do not rewrite the full docs.
4. Keep diagrams and file paths accurate. Only update diagrams if underlying
   flows changed.
5. Preserve tone and file organization; add new subsystem files only if the
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

---

## Output Constraints

- Update only files inside `projects/<submodule>/`.
- Use minimal diffs. No large rewrites.
- Prefer patching existing bullets and diagrams to preserve layout.
