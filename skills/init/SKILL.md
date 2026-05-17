---
name: init
description: Create a new DataTug project — scaffolds the project file layout in the current directory or a specified path. Use when starting a new data exploration project.
user-invocable: true
---

# datatug init

Wraps `datatug init` — scaffold a new DataTug project.

## Pre-flight check

```bash
command -v datatug >/dev/null 2>&1
```

On miss, invoke `/datatug:install` and stop until `datatug --help` succeeds.

## Command

```bash
datatug init
```

## When to use

- Bootstrapping a new DataTug project (queries, dashboards, pipelines, settings) at the current directory.

## Notes

- Creates the standard DataTug project layout (`datatug-project.yaml` and supporting directories).
- After init, register data sources with [`scan`](../scan/SKILL.md) and define datasets with [`datasets`](../datasets/SKILL.md).
