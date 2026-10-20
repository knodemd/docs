# 2 - Command line

Every command works without the app.

> Not released yet: these commands arrive with v0.1.

| Command | What it does |
|---|---|
| `knode init <name>` | Creates a space, a `<name>.knode` file in `~/Knode` |
| `knode search <words>` | Searches the space and prints the matching nodes |
| `knode import <path>` | Imports a folder of Markdown files (an export or an Obsidian vault), a Markdown file or an agent session file |
| `knode export <folder>` | Writes the space to a folder of Markdown files, one per note (v0.2) |
| `knode history [<node>]` | Shows the latest changes to the space, or to one node, and who made them (v0.2) |
| `knode undo <change>` | Undoes a change (v0.2) |
| `knode restore <node> <version>` | Puts a node back to an earlier version, or brings back a deleted one (v0.2) |
| `knode markdown folder <folder>` | Keeps a folder of Markdown files in step with the space (v0.2) |
| `knode connect <agent>` | Gives an agent its own key and adds Knode to it: `claude-code`, `codex`, `gemini`, `claude-desktop` |
| `knode agents <action>` | Lists agents, sets what each may do in each space, or revokes their keys |
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
knode history
knode export ~/Documents/work-notes
```

## Which space

A space is a `.knode` file. Every node in it is Markdown, so what you import keeps its text, its
links and its structure. See [the .knode format](05-format.md).

`knode search`, `knode import`, `knode export`, `knode history`, `knode undo`, `knode restore`,
`knode markdown` and `knode mcp` use, in this order:

1. the space given with `--space <path>`;
2. the space in the `KNODE_SPACE` environment variable;
3. your default space.

The first space you create becomes your default. Knode keeps it in `config.toml`, in the Knode
folder: `~/Library/Application Support/Knode` on macOS, `~/.local/share/knode` on Linux, or the
folder in `KNODE_HOME` if you set it.

```toml
default_space = "/Users/you/Knode/work.knode"
```

Every command that uses a space reads and writes through Knode Service, and starts it in the
background when it is not running. If it cannot start, the commands open the space directly and
print a warning; `knode mcp` does not, because agents only reach spaces through Knode Service,
which checks what each may do.

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
knode import <path> [--vault]
```

- **A folder:** every `.md` file in it and in its subfolders, leaving out hidden ones such as
  `.git`, `.obsidian` and `.trash`. Each file becomes a note titled with its file name, or with
  the `title` of its frontmatter; its text is kept as it is. A folder written by `knode export`
  brings back every note with its id, kind, tags, dates, authors, links and properties. An
  Obsidian vault keeps its frontmatter, tags and `[[links]]`. `--vault` only changes the wording
  of the summary. See [Markdown in and out](#markdown-in-and-out).
- **A Markdown file:** one note, titled with its first heading, or its file name when it has none.
- **A Claude Code or Codex session file** (`.jsonl`): one node of kind `conversation`, with its
  title, project and date, written as that agent.

Importing the same files again does not make copies: a file that changed updates its note. Files
that are not Markdown, such as images, are left out for now.

## export

```sh
knode export <folder>
```

From v0.2. Writes every node of the space to `<folder>` as a `.md` file, creating the folder if
needed, and prints how many files it wrote, left as they were, and removed. Exporting again to the
same folder rewrites only what changed. See [Markdown in and out](#markdown-in-and-out).

## history

```sh
knode history [<node>] [--limit <n>] [--json]
```

From v0.2. Without a node, lists the latest changes to the space, newest first: the change number,
when, what was done, to which note, and who did it, a person or an agent. With a node (its id or
exact title, even a deleted one by id), lists the changes to that node with the **version** each one
wrote. `--limit` (or `-n`) sets how many changes to show, 20 by default; `--json` prints them as
JSON.

```text
"Pricing decision"  01K83N6V4W5D2XQJ0Y7RZ9T1AB
  change 43     2026-10-20 09:31  undo  by you (person)  version 59  (undoes change 42)
  change 42     2026-10-20 09:30  node.update  by claude-code (agent)  version 58
  change 41     2026-10-20 09:12  node.create  by you (person)  version 57
```

## undo

```sh
knode undo <change> [--force]
```

From v0.2. Undoes a change, by its number from `knode history`: a note it created is deleted, a
note it changed or deleted goes back to how it was just before, and a link it made or removed is
put back. The undo is one more change, so nothing is lost: undoing it redoes the change.

If someone changed the same notes later, `knode undo` stops and names those changes, because
undoing would overwrite them. Check them with `knode history`, then pass `--force` to undo anyway.
Changes to settings are not undone: set them again instead.

## restore

```sh
knode restore <node> <version>
```

From v0.2. Puts a note back to one of its versions from `knode history <node>`, as a new change.
To bring back a deleted note, restore it to the version its delete kept: it comes back with the
same id, so links to it work again.

## markdown folder

```sh
knode markdown folder [<folder> | --off]
```

From v0.2. A **live Markdown folder** is a folder Knode keeps in step with a space, one way, from
the space to the folder: every note is a `.md` file there, ready for Obsidian, git or any editor.
It is off until you turn it on:

- `knode markdown folder ~/Documents/work-notes` turns it on, or moves it. Knode Service writes
  the whole space there, then each change within a few seconds.
- `knode markdown folder` shows where it is.
- `knode markdown folder --off` turns it off; the files stay as they are.

Each space has its own folder. Edits made in the folder are not read back: use `knode import` for
that. Knode warns you when the folder is inside iCloud Drive, Dropbox, OneDrive or Google Drive,
because two programs syncing the same files can make conflicted copies; a space syncs on its own.

## Markdown in and out

From v0.2. Every note is Markdown, so a space can always go to a folder of `.md` files and come back
without losing anything. `knode export` and the live Markdown folder write one file per note, named
after its title, with YAML frontmatter:

```markdown
---
id: 01K83N6V4W5D2XQJ0Y7RZ9T1AB
kind: decision
tags: [pricing]
created: 2026-10-20T09:12:00.000Z
created_by:
  kind: person
  actor: you
updated: 2026-10-20T09:30:41.511Z
updated_by:
  kind: agent
  actor: claude-code
links:
  part-of: [01K83N8Q2M4C7ZP1Y5X3RW0B9D]
---
We charge **per seat**. See [[Roadmap]].
```

- `[[links]]` show the current title of the note they point at, and are rewritten when it is
  renamed.
- Notes in subfolders of an imported vault are written back to the same subfolders.
- Knode only ever changes or removes files it wrote: your own files in the folder are never
  touched.
- `knode import` of an exported folder, into the same space or another one, keeps every note's id,
  dates and authors, so exporting the copy gives the same files.

## connect

```sh
knode connect <agent> [--space <path>] [--print]
```

Connecting an agent does two things:

1. **It gives the agent its own key.** The key is kept on this machine only: in the Keychain on
   macOS, in the Secret Service on Linux, or, where there is none, in a file in the Knode folder
   that only you can read. It is never written into the agent's settings or into a space. The
   first time, the agent may read and write your default space (or the one in `--space`) and
   nothing else; `knode connect` prints what it can do. Change it with `knode agents`.
2. **It adds Knode to the agent**, which then runs `knode mcp --agent <name>`:

| Agent | Name | What it does |
|---|---|---|
| `claude-code` | `claude-code` | Runs `claude mcp add --scope user knode -- <path to knode> mcp --agent claude-code` |
| `codex` | `codex` | Runs `codex mcp add knode -- <path to knode> mcp --agent codex`, or adds `[mcp_servers.knode]` to `~/.codex/config.toml` when `codex` is not installed |
| `gemini` | `gemini-cli` | Adds `knode` to `mcpServers` in `~/.gemini/settings.json` |
| `claude-desktop` | `claude-desktop` | Adds `knode` to `mcpServers` in `claude_desktop_config.json` |

When it edits a settings file, it changes only the `knode` entry, keeps a copy of the file as
`<file>.knode-backup` first, and does nothing if Knode is already there. `--print` shows what it
would do without doing it. If `claude` is not installed, it prints the command to run later.

Running it again keeps the agent's key and access. If the key is missing or was revoked, the agent
gets a new one and its old keys stop working.

### Agents connected before keys

Agents connected with an earlier version run `knode mcp` without a key. They stop working until you
connect them again: their tools answer with the command to run, such as
`knode connect claude-code`. `knode agents list` lists them too. Knode never gives them access by
itself.

## agents

```sh
knode agents list
knode agents grant <agent> <space> none|read|write
knode agents revoke <agent>
```

| Action | What it does |
|---|---|
| `list` | Lists the agents of this machine, whether their key is valid, and what each may do in each space |
| `grant` | Sets what an agent may do in a space: `none`, `read` or `write`. `<space>` is a `.knode` path or the name of a space in `~/Knode` |
| `revoke` | Revokes every key of the agent and takes away its access to every space. `knode connect` connects it again |

- **none:** the space is invisible to the agent. It is left out of everything the agent sees, and
  naming it fails as if it did not exist. An agent has `none` in every space you have not given it.
- **read:** the agent can search and read the space, but not change it.
- **write:** the agent can also create, edit, delete and link nodes.

A change takes effect on the agent's next call, without restarting it. Only you can change access,
from the command line or the app; agents cannot, whatever their level. Grants are kept inside each
space, so they travel with it to your other devices.

## mcp

```sh
knode mcp --agent <name> [--space <path>]
```

Runs the MCP server on standard input and output, for the agent `<name>`, with its key from this
machine's credential store. Agents start it themselves once you run `knode connect`. Instead of
`--agent`, the name can be in the `KNODE_AGENT` environment variable; on a machine without a
credential store, the key itself can be in `KNODE_AGENT_KEY`. Never pass a key on the command line.
See [Agents and MCP](03-mcp.md).

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
