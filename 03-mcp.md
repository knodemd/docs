# 3 - Agents and MCP

Knode gives every agent the same memory through MCP, the Model Context Protocol. Claude Code, Codex,
Gemini and Claude Desktop all read and write the same spaces.

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

`knode mcp` reads and writes through Knode Service, starting it in the background when it is not
running, so what one agent writes is seen at once by the others, by the app and by the command line.
Agents never open a space by themselves: if Knode Service cannot start, the tools say so.

## What agents can do

| Tool | What it does |
|---|---|
| `search` | Finds nodes by words, best match first, with a snippet of each |
| `read_node` | Reads a node in full, by id or title: its Markdown, kind, tags, links and backlinks |
| `write_node` | Creates a node, or edits one by id or title; can append to the end of the text |
| `link_nodes` | Links two nodes, by id or title: `related`, `part-of`, `derived-from` |
| `recent` | Lists the nodes that changed most recently |

Agents read and write Markdown, the same text you see in the app. A `[[Title]]` in the text links
to the node with that title. Every tool answers with Markdown for the agent to read and the same
data as structured content.

Every change an agent makes is kept with the name of the agent its key belongs to, such as
`claude-code`, never a name it gives itself, so you can always see who wrote what.

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
