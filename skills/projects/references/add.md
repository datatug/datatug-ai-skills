# datatug projects add

Register a DataTug project on this machine (in `~/.datatug.yaml`) so it can be referenced by id with `-p <id>`.

## Command

```bash
datatug projects add
```

The command is intended to register a project by **name and directory**.

> ⚠️ **Known limitation (as of CLI v0.x):** `datatug projects add` does not currently accept the name/directory as arguments — the inputs are unwired, so it cannot register a project from the command line yet. Track: <https://github.com/datatug/datatug-cli/issues/137>. Until it's fixed, add the project entry to `~/.datatug.yaml` manually (an `id` + `path` pair under `projects`).

## When to use

- After cloning a DataTug project repo from Git.
- After scaffolding a new project with [`init`](../../init/SKILL.md).

## Notes

- Run `datatug projects add --help` for the authoritative argument list once the command is fixed.
