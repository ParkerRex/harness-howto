# Project Doc Snapshots

This folder stores timestamped snapshots of the doc set before each update.
Snapshots are created by the `Update submodules & docs` GitHub workflow when
docs change.

## Layout

```
projects/_snapshots/<project>/<YYYYMMDD-HHMMSS>/
  SNAPSHOT_META.txt
  (archived docs)
```

## Metadata

Each snapshot includes `SNAPSHOT_META.txt` with:

- `snapshot_utc` - UTC timestamp for the snapshot folder.
- `source_ref` - commit SHA (short) of the previous doc set.
- `project` - project name.
- `doc_path` - path archived (manual docs or legacy file).

## Retention

Snapshots are kept in git history and can be pruned manually if needed.
