# 2 - Command line

Every command works without the app.

> Not released yet: these commands arrive with v0.1.

| Command | What it does |
|---|---|
| `knode init <name>` | Creates a space, a `<name>.knode` file in `~/Knode` |
| `knode search <words>` | Searches the space by words and meaning and prints the matching nodes |
| `knode index <action>` | Shows how far search by meaning has indexed the space, indexes it now, or turns it off (v0.3) |
| `knode import <path>` | Imports a Claude, ChatGPT or Gemini data export (v0.3), a folder of Markdown files (an export or an Obsidian vault), a Markdown file or an agent session file |
| `knode export <folder>` | Writes the space to a folder of Markdown files, one per note (v0.2) |
| `knode history [<node>]` | Shows the latest changes to the space, or to one node, and who made them (v0.2) |
| `knode undo <change>` | Undoes a change (v0.2) |
| `knode restore <node> <version>` | Puts a node back to an earlier version, or brings back a deleted one (v0.2) |
| `knode links <node>` | Shows the links of a node, each as it reads from the node, such as *part of* or *needed by* (v0.3) |
| `knode link <from> <to>` | Links two nodes, with a kind of link such as `part-of` (v0.3) |
| `knode kinds [<action>]` | Lists the kinds of link, or adds or removes one of the space's own (v0.3) |
| `knode attach <node> <file>` | Attaches a file to a node (v0.3) |
| `knode attachments <node>` | Lists the files attached to a node (v0.3) |
| `knode markdown folder <folder>` | Keeps a folder of Markdown files in step with the space, both ways from v0.3 (v0.2) |
| `knode connect <agent>` | Gives an agent its own key and adds Knode to it; `knode connect --list` shows every agent |
| `knode agents <action>` | Lists agents, sets what each may do in each space, or revokes their keys |
| `knode mcp` | Runs the MCP server; agents start it themselves |
| `knode spaces [<action>]` | Lists the spaces Knode knows, with their icons; renames one, sets its icon, makes it the default or turns capture into it on or off (v0.2) |
| `knode folders <action>` | Lists, adds and removes the folders that feed a space, such as the projects whose conversations it captures (v0.2) |
| `knode service <action>` | Runs, starts, stops or shows Knode Service, or starts it at login |
| `knode --version` | Prints the version |
| `knode --help` | Lists every command and option |

## Examples

```sh
knode init work
knode import ~/notes
knode search pricing decision
knode search --meaning how did we decide what to charge
knode connect claude-code
knode history
knode export ~/Documents/work-notes
```

## Which space

A space is a `.knode` file. Every node in it is Markdown, so what you import keeps its text, its
links and its structure. See [the .knode format](05-format.md).

`knode search`, `knode import`, `knode export`, `knode history`, `knode undo`, `knode restore`,
`knode markdown`, `knode folders` and `knode mcp` use, in this order:

1. the space given with `--space <space>`: its `.knode` path or, from v0.2, the name of a space
   Knode knows;
2. the space in the `KNODE_SPACE` environment variable;
3. your default space.

The first space you create becomes your default. Knode keeps it in `config.toml`, in the Knode
folder: `~/Library/Application Support/Knode` on macOS, `~/.local/share/knode` on Linux, or the
folder in `KNODE_HOME` if you set it.

```toml
default_space = "/Users/you/Knode/work.knode"
spaces = ["/Users/you/Knode/personal.knode"]
```

`spaces` lists the other spaces Knode knows. `knode init` adds each new space to it, and the app
does the same.

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

## spaces

```sh
knode spaces [--json]
knode spaces rename <space> <name>
knode spaces icon <space> <emoji> | --clear
knode spaces default <space>
knode spaces capture <space> on|off
```

From v0.2. Without an action, lists every space Knode knows: those in `config.toml` and those in
`~/Knode`. The default space is marked with `*`; `--json` prints them as JSON.

```text
* 💼 Work      /Users/you/Knode/work.knode  (default)
  🌱 Personal  /Users/you/Knode/personal.knode
```

`<space>` is a space's name or its `.knode` path.

| Action | What it does |
|---|---|
| `rename` | Renames the space. Its file keeps its name, so nothing that points at it breaks |
| `icon` | Sets the icon of the space, one emoji, or removes it with `--clear` |
| `default` | Makes it your default space. The previous default stays in the list |
| `capture` | Turns capture of agent conversations into the space on or off. Off, the conversations of its projects are stored nowhere |

The name, the icon and the capture setting are kept inside the space, so they travel with it to
your other devices. Only you can change them, from the command line or the app; agents cannot.

## folders

```sh
knode folders list [--json]
knode folders add <path> [--kind project|notes|agent] [--capture on|off] [--exclude <pattern>]... [--agent <agent>]
knode folders remove <path or id>
```

From v0.2. The folders that feed a space, given with `--space` or your default space:

- **project** (the default): a folder you and your agents work in, such as a code repository.
  Agent conversations held in it, or in any folder inside it, are captured into this space. With
  `--capture off`, nothing from them is stored at all, in this space or any other.
- **notes:** a folder of Markdown notes for the space.
- **agent:** a folder where an agent keeps its sessions, read besides the agent's usual one. Name
  the agent with `--agent`, such as `--agent claude-code`.

`--exclude` leaves out folders inside it, with patterns such as `node_modules/**`: `*` matches any
characters but `/`, `**` any number of folders, and `?` one character. Repeat it for several.

```sh
knode folders add ~/code/site --space Work
knode folders add ~/code/site/drafts --space Work --capture off
knode folders list --space Work
```

Adding the same folder again changes it. Removing a folder keeps everything already captured from
it. A folder inside your home folder is kept as `~/...`, so the space finds it on your other Macs
even when your user name differs there; a folder that is not on this machine is kept and marked
`missing here`.

## Spaces and folders

From v0.2. You can keep several spaces, such as one for work and one for personal life, and decide
where each agent conversation goes by the folder it was held in. Knode Service decides, for each
conversation:

1. It looks for the project folders, in every space Knode knows, that hold the folder of the
   conversation, and keeps the most specific one: `~/code/site/app` wins over `~/code`. The same
   folder can be a project of two spaces, and then the conversation goes to both.
2. The conversation is stored in those spaces, unless capture is off for the project, one of its
   `--exclude` patterns leaves the folder out, or capture is off for the space. If none of them
   takes it, it is **stored nowhere**: turning capture off for a project never sends its
   conversations somewhere else.
3. A conversation held in no project goes to the space of the agent folder it was read from, if
   any, or else to your default space.

`capture = false` and `capture_exclude` in `config.toml` still work for the whole machine, whatever
the spaces say. A change to folders or settings applies to the next message an agent writes, without
restarting anything.

## search

```sh
knode search <words> [--meaning | --words] [--limit <n>] [--json]
```

Prints the best matches first, each with its kind, date, who changed it last, a snippet and its
id. By default it ranks matches by words and by meaning together, so it also finds notes that
say the same thing in other words, or in another language (v0.3, see [Search](#search-by-words-and-meaning)).

- `--words` searches by the words only: every word must appear, and the last one also matches
  the start of a word. Case and accents are ignored. Best for names, ids and exact terms.
- `--meaning` searches by meaning only: notes about what you asked, whatever words they use.
- `--limit` (or `-n`) sets how many nodes to print, 10 by default.
- `--json` prints `{ "mode", "hits", "note" }`: the mode that ran, the matches, and why the
  search ran by words when meaning was asked for but is not ready yet.

When search by meaning is not ready yet (the model is still downloading, or the space is still
being indexed), the search runs by words and prints a note saying why.

## index

```sh
knode index <status|run|off>
```

| Action | What it does |
|---|---|
| `status` | Shows whether search by meaning is on, the search model (downloading, ready) and how many notes of the space wait to be indexed. `--json` prints it as JSON |
| `run` | Turns search by meaning on and indexes the space now, even on battery, showing its progress until it is done. The first time, it waits for the model to download |
| `off` | Turns search by meaning off: nothing is downloaded or indexed, and search is by words. What is already indexed stays |

You rarely need these: Knode Service indexes in the background by itself.

## Search by words and meaning

Search by meaning finds notes by what they are about, not only by the words they share with
your query: "how did we decide what to charge" finds the note titled "Pricing". It runs on your
machine, with a small open model ([multilingual-e5-small](https://huggingface.co/intfloat/multilingual-e5-small),
about 100 languages). Nothing you write or search for is sent anywhere. (v0.3)

- **The first time** there is something to index, Knode Service downloads the model once, about
  490 MB, in the background, and checks every file. It is kept in the Knode home folder, in
  `models`. Until it is ready, search is by words.
- **Indexing** runs in the background: when a space opens, and a couple of seconds after each
  change. It never slows down writing, and it pauses while your laptop runs on battery (`knode
  index run` indexes anyway). A note that is not indexed yet is still found by its words.
- **Turning it off:** `knode index off`, or in `config.toml`:

  ```toml
  embeddings = "off"
  ```

  `knode index run` turns it on again. `KNODE_EMBEDDINGS=off` in the environment of Knode Service
  turns it off too.
- **Raspberry Pi and other ARM Linux machines:** search by meaning is not available there yet, so
  search is by words.

`knode service status` shows the same as `knode index status`.

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
- **A data export** (from v0.3): the `.zip` you download from Claude, ChatGPT or Google Takeout
  (Gemini Apps), or the folder it was extracted to. Knode tells which one it is by what is inside.
  Each conversation becomes a node of kind `conversation`, with its title, model and dates; Claude
  projects become notes, and their conversations link to them as `part-of`. It prints how many
  conversations are new, updated and unchanged, and anything it skipped and why. Importing the
  same export again, or a newer one of the same account, updates the conversations instead of
  copying them.

  ```sh
  knode import ~/Downloads/data-2026-10-20.zip
  ```

A path that is none of these fails and says what `knode import` takes.

Importing the same files again does not make copies: a file that changed updates its note. From
v0.3, a file that is not Markdown, such as an image, is attached to the note that embeds it with
`![[name]]` (or links to it with `[[name]]`) when it is in the same folder as the note; other such
files are left out. A kind of link the space does not have yet is added to it, so no link is lost.

## links

```sh
knode links <node> [--json]
```

From v0.3. Shows the links of a node, by id or exact title: first the links going out (`->`), then
the ones coming in (`<-`), each with how it reads from this node, the kind and the id of the node
at the other end. A link `App` *depends on* `Database` shows as `-> depends on "Database"` on
`App` and as `<- needed by "App"` on `Database`. A `[[link]]` in the text shows as *mentions* and
*mentioned in*.

## link

```sh
knode link <from> <to> [--kind <kind>]
```

From v0.3. Links two nodes, by id or exact title, with a kind of link: `related` by default, or
any kind `knode kinds` lists. Linking twice does nothing more. `mentions` links come only from
`[[links]]` in a note's text.

## kinds

```sh
knode kinds [list] [--json]
knode kinds add <kind> --label <label> [--inverse <label>] [--symmetric] [--description <text>]
knode kinds remove <kind>
```

From v0.3. A kind of link says how two nodes relate, and reads one way from each end. Every space
has the built-in kinds:

| Kind | From the source | From the target |
|---|---|---|
| `mentions` | mentions | mentioned in |
| `related` | related to | related to |
| `part-of` | part of | has part |
| `derived-from` | derived from | source of |
| `answers` | answers | answered by |
| `contradicts` | contradicts | contradicted by |

`knode kinds add` defines a kind of the space's own, or changes its labels: a name of lower case
letters, digits and `-`, how it reads from its source (`--label`) and from its target
(`--inverse`), or `--symmetric` for a kind that reads the same from both ends. `--description`
says what it means, for you and for agents choosing a kind. `knode kinds remove` removes a kind
the space defined, once no link has it. Built-in kinds cannot be changed or removed.

```sh
knode kinds add depends-on --label "depends on" --inverse "needed by"
knode link App Database --kind depends-on
```

## attach

```sh
knode attach <node> <file>
```

From v0.3. Attaches a file, such as an image or a PDF, to a node, by id or exact title, under the
file's name, up to 100 MB. The file is copied into the space, stored once however many nodes use
it. `![[name]]` in the node's text shows it. Attaching a different file with a name the node
already uses adds ` 2`, ` 3`... to the name.

## attachments

```sh
knode attachments <node> [--json]
```

From v0.3. Lists the files attached to a node, with their type, size, date and who attached them.

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

From v0.2. A **live Markdown folder** is a folder Knode keeps in step with a space: every note is a
`.md` file there, ready for Obsidian, git or any editor. It is off until you turn it on:

- `knode markdown folder ~/Documents/work-notes` turns it on, or moves it. Knode Service writes
  the whole space there, then each change within a few seconds.
- `knode markdown folder` shows where it is.
- `knode markdown folder --off` turns it off; the files stay as they are.

From v0.3 it works both ways. The space stays the source of truth, and Knode Service watches the
folder and reads each edit made to its files back in as one more edit by you, with the file as its
source, so it is in the history like any other:

- a changed file updates its note, and an edit made in the folder while Knode was not running comes
  in the next time the space opens;
- a new file becomes a note, and gets an `id` in its frontmatter;
- a renamed or moved file renames or moves its note;
- a deleted file never deletes its note: the note is marked `markdown.deleted` and kept until you
  decide;
- `[[Title]]` links in the files become links between notes.

When a note changed in Knode and in the folder since the file was last written, each field keeps
the side that changed it. A body changed on both sides is merged when the note is being edited
together (see [Editing together](04-mac-app.md#editing-together-v03)); otherwise the side changed last wins and the other
version stays in the history.

Each space has its own folder. Knode warns you when the folder is inside iCloud Drive, Dropbox,
OneDrive or Google Drive, because two programs syncing the same files can make conflicted copies; a
space syncs on its own.

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
- From v0.3, attachments are written next to their note, under their names, so `![[beds.svg]]`
  finds them in any Markdown editor. When two notes in one folder have different files with the
  same name, the second is written as `beds 2.svg`; `.knode/files.json` in the folder remembers
  which is which.
- From v0.3, the kinds of link your space defines are written to `.knode/link-kinds.json`, with
  their labels.
- `knode import` of an exported folder, into the same space or another one, keeps every note's id,
  dates, authors, attachments and kinds of link, so exporting the copy gives the same files.

## connect

```sh
knode connect <agent> [--space <path>] [--print]
knode connect --list
```

`--list` shows every agent Knode can be added to, whether it is on this machine, and the file it
would change.

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
| `cursor` | `cursor` | Adds `knode` to `mcpServers` in `~/.cursor/mcp.json` |
| `windsurf` | `windsurf` | Adds `knode` to `mcpServers` in Windsurf's `mcp_config.json` |
| `cline` | `cline` | Adds `knode` to `mcpServers` in `cline_mcp_settings.json`, in every editor Cline is installed in and in Cline CLI |
| `roo-code` | `roo-code` | Adds `knode` to `mcpServers` in `mcp_settings.json`, in every editor Roo Code is installed in |
| `copilot-cli` | `copilot-cli` | Adds `knode` to `mcpServers` in `~/.copilot/mcp-config.json` |
| `vscode` | `vscode` | Adds `knode` to `servers` in the VS Code user `mcp.json`, for GitHub Copilot agent mode |
| `opencode` | `opencode` | Adds `knode` to `mcp` in `~/.config/opencode/opencode.json` |
| `amp` | `amp` | Adds `knode` to `amp.mcpServers` in `~/.config/amp/settings.json` |
| `zed` | `zed` | Adds `knode` to `context_servers` in `~/.config/zed/settings.json` |
| `aider` | - | Changes nothing and gives no key: Aider has no MCP client, so it prints what to do instead |

When it edits a settings file, it changes only the `knode` entry, keeps a copy of the file as
`<file>.knode-backup` first, keeps its permissions and, where the agent allows them, its comments,
and does nothing if Knode is already there. A file the agent could not read is left untouched.
`--print` shows what it would do without doing it. If `claude` is not installed, it prints the
command to run later. [Agents and MCP](03-mcp.md#where-each-agent-is-configured) lists every file
on macOS and Linux.

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
| `status` | Shows its version, process id, open spaces, connected clients, the default space conversations are captured into, how far search by meaning has indexed the spaces, and whether it starts at login |
| `install` | Starts it at login, and now: a launch agent on macOS, a systemd user unit on Linux |
| `uninstall` | Stops starting it at login, and stops it |

After `install`, Knode Service starts again by itself if it crashes. After `knode service stop` it
stays stopped until your next login, or until a `knode` command or an agent needs it.

### Capture

While Knode Service runs, every agent conversation becomes a node of kind `conversation`, kept
current while the agent writes it, in the space its project belongs to, or else in your default
space: see [Spaces and folders](#spaces-and-folders). To turn it off on this machine, or to leave
some projects out whatever the spaces say, add to `config.toml`:

```toml
capture = false
capture_exclude = ["/Users/you/code/secret-project"]
```

Leaving out a folder leaves out every project inside it. Knode Service reads `config.toml` again
within a few seconds, so there is no need to restart it.
