# 3 - Agents and MCP

Knode gives every agent the same memory through MCP, the Model Context Protocol. Claude Code, Codex,
Gemini and Claude Desktop all read and write the same spaces.

> Not released yet: arrives with v0.1.

## Connect

```sh
knode connect claude-code
```

See [Install](01-install.md) for every agent, and [the command line](02-cli.md#connect) for what
`knode connect` changes. The server itself is `knode mcp`, which agents start on their own; it
uses your default space, or the one in `--space <path>` or `KNODE_SPACE`.

`knode mcp` reads and writes through Knode Service, starting it in the background when it is not
running, so what one agent writes is seen at once by the others, by the app and by the command line.
If Knode Service cannot start, the server opens the space directly and prints a warning on its
standard error, which agents keep in their logs.

## What agents can do

| Tool | What it does |
|---|---|
| `search` | Finds nodes by words, best match first, with a snippet of each |
| `read_node` | Reads a node in full, by id or title: its Markdown, kind, tags, links and backlinks |
| `write_node` | Creates a node, or edits one by id or title; can append to the end of the text |
| `link_nodes` | Links two nodes, by id or title: `related`, `part-of`, `derived-from` |
| `recent` | Lists the nodes that changed most recently |
| `history` | Lists the latest changes to a node or to the whole space, with who made them (v0.2) |
| `undo` | Undoes a change the same agent made (v0.2) |

Agents read and write Markdown, the same text you see in the app. A `[[Title]]` in the text links
to the node with that title. Every tool answers with Markdown for the agent to read and the same
data as structured content.

Every change an agent makes is kept with its name, such as `claude-code`, so you can always see
who wrote what.

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

From v0.2, each agent gets access per space: read, write or nothing.
