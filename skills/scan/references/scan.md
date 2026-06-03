# datatug scan

Introspect a configured database and write its schema metadata into the DataTug project. Runs DDL discovery against the source and updates the project's metadata files.

## Command

```bash
datatug scan -p <project> --env <ENV> --db <database-id> -s <server> -D <driver> [-U <user> -P <password> --port <n> --dbmodel <id>]
```

There is **no `--source` flag** — the source is specified by the connection flags below.

## Parameters

| Flag | Required | Description |
|---|---|---|
| `-p`, `--project <name>` | Yes* | Project to write metadata into. |
| `-d`, `--directory <path>` | Yes* | Alternative to `-p`: path to the project directory. |
| `--env <ENV>` | Yes | Environment the DB belongs to (e.g. `LOCAL`, `DEV`, `UAT`, `PROD`). |
| `--db <id>` | Yes | Id of the database to scan. |
| `-D`, `--driver <name>` | — | DB driver (e.g. `sqlserver`). |
| `-s`, `--server <host>` | — | Network server / host name. |
| `--port <n>` | — | Server port (default if omitted). |
| `-U`, `--user <name>` | — | DB login user. |
| `-P`, `--password <pwd>` | — | DB login password. |
| `--dbmodel <id>` | — | DB model id; required for newly scanned databases. |

\* Provide **either** `-p` or `-d`.

## When to use

- After adding a new database connection.
- After upstream schema changes (new tables, dropped columns).

## Notes

- Run `datatug scan --help` for the authoritative flag list.
- Writes to project metadata files — commit the diff to version-control the schema snapshot.

> ⚠️ **Known limitation (as of CLI v0.x):** `scan` is partially unimplemented — it panics (`not implemented yet`) when no server/host is supplied and for the `sqlite3` driver. Treat scan as work-in-progress until fixed. Track: <https://github.com/datatug/datatug-cli/issues/136>.
