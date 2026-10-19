# 2 - Command line

Every command works without the app.

> Not released yet: these commands arrive with v0.1.

| Command | What it does |
|---|---|
| `knode init <name>` | Creates a space, a `<name>.knode` file in `~/Knode` |
| `knode search <words>` | Searches the space and prints the matching nodes |
| `knode import <path>` | Imports a folder of Markdown files, a Markdown file or an agent session file |
| `knode connect <agent>` | Adds Knode to an agent: `claude-code`, `codex`, `gemini`, `claude-desktop` |
| `knode mcp` | Runs the MCP server; agents start it themselves |
| `knode spaces` | Lists the spaces Knode knows about |
| `knode service <action>` | Runs, starts, stops or shows Knode Service, or starts it at login |
| `knode --version` | Prints the version |
| `knode --help` | Lists every command and option |

## Examples

```sh
knode init work
knode import ~/notes
knode search pricing decision
knode connect claude-code
```

## Which space

A space is a `.knode` file. Every node in it is Markdown, so what you import keeps its text, its
links and its structure. See [the .knode format](05-format.md).

`knode search`, `knode import` and `knode mcp` use, in this order:

1. the space given with `--space <path>`;
2. the space in the `KNODE_SPACE` environment variable;
3. your default space.

The first space you create becomes your default. Knode keeps it in `config.toml`, in the Knode
folder: `~/Library/Application Support/Knode` on macOS, `~/.local/share/knode` on Linux, or the
folder in `KNODE_HOME` if you set it.

```toml
default_space = "/Users/you/Knode/work.knode"
```

`knode search`, `knode import`, `knode init` and `knode mcp` read and write through Knode Service,
and start it in the background when it is not running. If it cannot start, they open the space
directly and print a warning.

## init

```sh
knode init <name> [--dir <folder>] [--default]
```

Creates `<name>.knode` in `~/Knode`, or in `--dir`. It becomes your default space if you have none
yet, or if you pass `--default`.

## search

```sh
knode search <words> [--limit <n>] [--json]
```

Prints the best matches first, each with its kind, date, who changed it last, a snippet and its
id. Every word must appear; the last one also matches the start of a word. Case and accents are
ignored. `--limit` (or `-n`) sets how many nodes to print, 10 by default; `--json` prints them as
JSON.

## import

```sh
knode import <path>
```

- **A folder:** every `.md` file in it and in its subfolders, leaving out hidden ones such as
  `.git` and `.obsidian`. Each file becomes a note: its first heading is the title, or the file
  name when it has none, and its text is kept as it is.
- **A Markdown file:** one note, the same way.
- **A Claude Code or Codex session file** (`.jsonl`): one node of kind `conversation`, with its
  title, project and date, written as that agent.

Importing the same files again does not make copies.

## connect

```sh
knode connect <agent> [--print]
```

| Agent | What it does |
|---|---|
| `claude-code` | Runs `claude mcp add --scope user knode -- <path to knode> mcp` |
| `codex` | Runs `codex mcp add knode -- <path to knode> mcp`, or adds `[mcp_servers.knode]` to `~/.codex/config.toml` when `codex` is not installed |
| `gemini` | Adds `knode` to `mcpServers` in `~/.gemini/settings.json` |
| `claude-desktop` | Adds `knode` to `mcpServers` in `claude_desktop_config.json` |

When it edits a settings file, it changes only the `knode` entry, keeps a copy of the file as
`<file>.knode-backup` first, and does nothing if Knode is already there. `--print` shows what it
would do without doing it. If `claude` is not installed, it prints the command to run later.

## mcp

```sh
knode mcp [--space <path>]
```

Runs the MCP server on standard input and output. Agents start it themselves once you run
`knode connect`. See [Agents and MCP](03-mcp.md).

## service

```sh
knode service <run|start|stop|status|install|uninstall>
```

| Action | What it does |
|---|---|
| `run` | Runs Knode Service in the foreground until you press Ctrl-C. If it is already running, says so |
| `start` | Starts it in the background if it is not running |
| `stop` | Stops it, however it was started |
| `status` | Shows its version, process id, open spaces, connected clients, where conversations are captured, and whether it starts at login |
| `install` | Starts it at login, and now: a launch agent on macOS, a systemd user unit on Linux |
| `uninstall` | Stops starting it at login, and stops it |

After `install`, Knode Service starts again by itself if it crashes. After `knode service stop` it
stays stopped until your next login, or until a `knode` command or an agent needs it.

### Capture

While Knode Service runs, every Claude Code and Codex conversation becomes a node of kind
`conversation` in your default space, kept current while the agent writes it. To turn it off, or to
leave some projects out, add to `config.toml`:

```toml
capture = false
capture_exclude = ["/Users/you/code/secret-project"]
```

Leaving out a folder leaves out every project inside it. Knode Service reads `config.toml` again
within a few seconds, so there is no need to restart it.
