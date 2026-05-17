# datatug dataset-data

Print a dataset's data — the result of executing its underlying query.

## Command

```bash
datatug dataset-data <dataset-id> [--format <yaml|json|grid>]
```

## Parameters

| Flag | Required | Description |
|---|---|---|
| `--format` | No | Output format. `grid` is human-readable; `yaml`/`json` are machine-readable. |

## When to use

- Reading dataset rows from the CLI.
- Piping rows into another tool (`--format=json` and `jq`).

## Notes

- Read-only.
