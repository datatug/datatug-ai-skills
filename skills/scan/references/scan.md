# datatug scan

Introspect a configured database and write its schema metadata into the DataTug project. Runs DDL discovery against the source and updates the project's metadata files.

## Command

```bash
datatug scan [--source <id>]
```

## When to use

- After adding a new database connection.
- After upstream schema changes (new tables, dropped columns).

## Notes

- Run `datatug scan --help` for the authoritative flag list.
- Writes to project metadata files — commit the diff to version-control the schema snapshot.
