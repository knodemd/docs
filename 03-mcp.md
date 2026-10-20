# 3 - Agents and MCP

Knode gives every agent the same memory through MCP, the Model Context Protocol. Claude Code, Codex,
Gemini, Claude Desktop, Cursor, Windsurf, Cline, Roo Code, GitHub Copilot, OpenCode, Amp and Zed
all read and write the same spaces.

> Not released yet: arrives with v0.1.

## Connect

```sh
knode connect claude-code
```

See [Install](01-install.md) for every agent, and [the command line](02-cli.md#connect) for what
`knode connect` changes. It gives the agent its own key, kept in your machine's credential store,
and lets it read and write your default space only. The server itself is `knode mcp --agent
<name>`, which agents start on their own; it uses your default space, or the one in
`--space <path>` or `KNODE_SPACE`.

### Where each agent is configured

`knode connect --list` shows every agent, whether it is on this machine, and the file it would
change. On macOS and Linux:

| Agent | What `knode connect` changes |
|---|---|
| `claude-code` | Runs `claude mcp add --scope user knode -- <path to knode> mcp --agent claude-code` |
| `codex` | Runs `codex mcp add`, or adds `[mcp_servers.knode]` to `~/.codex/config.toml` |
| `gemini` | `mcpServers` in `~/.gemini/settings.json` |
| `claude-desktop` | `mcpServers` in `claude_desktop_config.json` |
| `cursor` | `mcpServers` in `~/.cursor/mcp.json` |
| `windsurf` | `mcpServers` in `~/.config/devin/mcp_config.json`, and in `~/.codeium/windsurf/mcp_config.json` when an earlier Windsurf is installed |
| `cline` | `mcpServers` in `cline_mcp_settings.json` of every editor Cline is installed in, and of Cline CLI (`~/.cline`) |
| `roo-code` | `mcpServers` in `mcp_settings.json` of every editor Roo Code is installed in |
| `copilot-cli` | `mcpServers` in `~/.copilot/mcp-config.json` (or `$COPILOT_HOME`) |
| `vscode` | `servers` in your VS Code user `mcp.json` (and VS Code Insiders') |
| `opencode` | `mcp` in `~/.config/opencode/opencode.json`, or `opencode.jsonc` |
| `amp` | `amp.mcpServers` in `~/.config/amp/settings.json`, or `settings.jsonc` |
| `zed` | `context_servers` in `~/.config/zed/settings.json` |
| `aider` | Nothing: Aider has no MCP client yet |

Every time, only the `knode` entry is added or replaced. The file is copied to
`<file>.knode-backup` first and keeps its permissions, and nothing changes if Knode is already
there. Where the agent accepts comments in its settings (VS Code, Zed, OpenCode and Amp), your
comments are kept. A file the agent itself could not read, such as one with a typo, is left
untouched and `knode connect` says what is wrong. Cline and Roo Code are only set up in editors
where they are installed; if there are none, nothing is changed.

**Aider** cannot run MCP servers yet. To give it what is in Knode, keep a live Markdown copy of a
space with `knode markdown folder <dir>` and add notes with `aider --read <dir>/<note>.md`, or run
`/run knode search <words>` inside Aider.

## How it works

`knode mcp` reads and writes through Knode Service, starting it in the background when it is not
running, so what one agent writes is seen at once by the others, by the app and by the command line.
Agents never open a space by themselves: if Knode Service cannot start, the tools say so.

## What agents can do

| Tool | What it does |
|---|---|
| `search` | Finds nodes by words, best match first, with a snippet of each |
| `read_node` | Reads a node in full, by id or title: its Markdown, kind, tags, links and backlinks (each as it reads from the node, from v0.3) and attached files (v0.3) |
| `write_node` | Creates a node, or edits one by id or title; can append to the end of the text |
| `link_nodes` | Links two nodes, by id or title, with a kind of link: `related`, `part-of`, `derived-from`, and from v0.3 `answers`, `contradicts` or a kind of the space's own |
| `recent` | Lists the nodes that changed most recently |
| `history` | Lists the latest changes to a node or to the whole space, with who made them (v0.2) |
| `undo` | Undoes a change the same agent made (v0.2) |
| `link_kinds` | Lists the kinds of link, with how each reads and what it means (v0.3) |
| `read_canvas` | Lists the canvases, or reads one: its cards with their positions and groups, and the links between them (v0.3) |
| `attach_file` | Attaches a file to a node: its bytes, or a file in a notes folder of the space (v0.3) |
| `read_attachment` | Reads a file attached to a node; text files come back as text (v0.3) |

Agents read and write Markdown, the same text you see in the app. A `[[Title]]` in the text links
to the node with that title. Every tool answers with Markdown for the agent to read and the same
data as structured content.

Every change an agent makes is kept with the name of the agent its key belongs to, such as
`claude-code`, never a name it gives itself, so you can always see who wrote what.

## Files

From v0.3, agents can attach files to nodes, such as a diagram they drew or a screenshot, and read
the ones you attached:

- An agent with `write` sends the bytes of the file, up to 10 MB.
- An agent can also attach a file by its path, but only a file inside a folder of kind `notes`
  that you added to the space (`knode folders add <dir> --kind notes`), and never a hidden file or
  one in a hidden folder. Any other path is refused. Larger files you attach yourself with
  [`knode attach`](02-cli.md#attach).
- `read_attachment` returns text files (Markdown, plain text, CSV, JSON, SVG and the like) as
  text, up to 256 KB; for other files it says what they are.

## History and undo

From v0.2, every change is kept, so any of them can be undone. Agents see the changes with
`history`, for one node or for the whole space.

An agent can only `undo` its own changes, and only when nobody changed the same notes after it.
Anything else it leaves to you: it tells you the change number, and you decide with
`knode history` and `knode undo` (see [the command line](02-cli.md#undo)). Like every undo, it is
one more change, so nothing is lost.

## Conversations

From v0.1, while Knode Service runs, every Claude Code and Codex conversation is kept as a node in
your default space, with its project and date, and updated while the conversation goes on. You can
turn this off, or leave projects out, in `config.toml`; see
[the command line](02-cli.md#capture). Per-space settings and other agents follow in v0.2. To bring
in one session by hand:

```sh
knode import ~/.claude/projects/<project>/<session>.jsonl
```

## Permissions

Each agent has its own key and, in each space, one level: `none`, `read` or `write`.

```sh
knode agents list
knode agents grant claude-code personal read
knode agents grant codex work none
knode agents revoke codex
```

- A space an agent may not see is **invisible** to it, not just empty: it is left out of search
  and of what the agent is told about, and naming it fails as if it did not exist. A work agent
  never learns that your personal space is there.
- With `read`, the agent can search and read; writing fails with a message it can show you.
- Knode Service checks every call, so a change takes effect on the agent's next call, and a
  revoked key is refused at once.
- Only you change access, from the command line or the app. No agent can, whatever its level.
- Grants live inside each space and travel with it. Keys never do: they stay on the machine where
  they were made, and spaces only hold a fingerprint of each.
- Knode keeps a log of each call an agent makes, on this machine only: when, which agent, which
  space, which tool and whether it was allowed. Never what was read or searched for.

See [the command line](02-cli.md#agents) for every option. Grants keep each agent to the spaces you
gave it through Knode; they are not a sandbox for programs that can read your files directly.
