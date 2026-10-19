# 3 - Agents and MCP

Knode gives every agent the same memory through MCP, the Model Context Protocol. Claude Code, Codex,
Gemini and Claude Desktop all read and write the same spaces.

> Not released yet: arrives with v0.1.

## Connect

```sh
knode connect claude-code
```

See [Install](01-install.md) for every agent.

## What agents can do

| Tool | What it does |
|---|---|
| `search` | Finds nodes by words |
| `read` | Reads a node as Markdown |
| `write` | Creates or edits a node |
| `link` | Links two nodes |
| `recent` | Lists what changed lately |

Agents read and write Markdown, the same text you see in the app.

## Conversations

From v0.1, every Claude Code and Codex conversation is kept as a node, with its project and date.
You can turn this off per project. Other agents follow in v0.2.

## Permissions

From v0.2, each agent gets access per space: read, write or nothing.
