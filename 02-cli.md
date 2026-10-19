# 2 - Command line

Every command works without the app. If Knode Service is not running, `knode` starts it in the
background, so only one process ever opens a space.

> Not released yet: these commands arrive with v0.1.

| Command | What it does |
|---|---|
| `knode init <name>` | Creates a space, a `<name>.knode` file |
| `knode search "<words>"` | Searches the space and prints the matching nodes |
| `knode import <path>` | Imports a folder of Markdown files or an agent session file |
| `knode connect <agent>` | Adds Knode to an agent: `claude-code`, `codex`, `gemini`, `claude-desktop` |
| `knode mcp` | Runs the MCP server; agents start it themselves |
| `knode --help` | Lists every command and option |

## Examples

```sh
knode init work
knode import ~/notes
knode search "pricing decision"
```

## Spaces

A space is a `.knode` file. Every node in it is Markdown, so what you import keeps its text, its
links and its structure. See [the .knode format](05-format.md).
