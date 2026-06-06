# DataTug AI Plugin

AI plugin for [DataTug](https://datatug.io) - skills that teach AI agents how to use the `datatug` CLI for data exploration, dataset management, schema scanning, and query execution.

This repository contains the plugin source. It is installed on top of the [`datatug` CLI](https://github.com/datatug/datatug-cli); the CLI is a prerequisite.

## Contents

| Directory | Description |
|---|---|
| [`skills/`](skills/README.md) | Agent skills - one per major `datatug` CLI surface area, progressively loaded per-verb |
| [`commands/`](commands/install.md) | Slash-command aliases for skills |
| [`.claude-plugin/`](.claude-plugin/plugin.json) | Claude Code plugin manifest |

## Install

Via the [DataTug AI marketplace](https://github.com/datatug/ai-marketplace):

```
/plugin marketplace add datatug/ai-marketplace
/plugin install datatug@datatug
```

## First use

The `datatug` CLI must be on your `PATH` before any wrapper skill can run. Options:

- Invoke `/datatug:install` inside Claude Code - the [install skill](skills/install/SKILL.md) shows the platform-appropriate install commands.
- Or install directly per the [DataTug CLI installation guide](https://github.com/datatug/datatug-cli#installation).

Verify with `datatug --help`.

## Relationship to the CLI

The plugin wraps the `datatug` CLI - it does not replace it. Skills encode *when* to call a command, *which* flags to pass, and *how* to interpret exit codes. The CLI source of truth is [`datatug/datatug-cli`](https://github.com/datatug/datatug-cli).

A change in the CLI surface typically produces a matching skill update in this repository; the two evolve together but release independently.

## Relationship to other plugins

`datatug` is a base-layer **CLI wrapper** plugin, in the same shape as:

- [`specscore`](https://github.com/specscore/ai-plugin-specscore)
- [`synchestra`](https://github.com/synchestra-io/ai-plugin-synchestra)
- [`ingitdb`](https://github.com/ingitdb/ingitdb-ai-skills)

## Releases

Releases are tagged as `datatug--v<version>` on this repository to support Claude Code's dependency resolution.

## License

MIT - see [LICENSE](LICENSE).
