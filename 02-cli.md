# 2 - Command line

Every command works without the app.

> Not released yet: these commands arrive with v0.1.

| Command | What it does |
|---|---|
| `knode init <name>` | Creates a space, a `<name>.knode` file in `~/Knode` |
| `knode search <words>` | Searches the space by words and meaning and prints the matching nodes |
| `knode index <action>` | Shows how far search by meaning has indexed the space, indexes it now, or turns it off (v0.3) |
| `knode import <path or URL>` | Imports a folder of Markdown files (an export or an Obsidian vault), a Markdown file or an agent session file, and from v0.3 a Claude, ChatGPT or Gemini data export, a PDF, a web page, Kindle or Readwise highlights, notes from Apple Notes, Bear, Notion or Evernote, mail from Gmail (Google Takeout) or Apple Mail, or a WhatsApp, Telegram or Slack chat export. See [Importing](#importing) |
| `knode export <folder>` | Writes the space to a folder of Markdown files, one per note (v0.2); `--compare <vault>` lists what differs from the folder you imported (v0.3) |
| `knode history [<node>]` | Shows the latest changes to the space, or to one node, and who made them (v0.2) |
| `knode undo <change>` | Undoes a change (v0.2) |
| `knode restore <node> <version>` | Puts a node back to an earlier version, or brings back a deleted one (v0.2) |
| `knode links <node>` | Shows the links of a node, each as it reads from the node, such as *part of* or *needed by* (v0.3) |
| `knode link <from> <to>` | Links two nodes, with a kind of link such as `part-of` (v0.3) |
| `knode kinds [<action>]` | Lists the kinds of link, or adds or removes one of the space's own (v0.3) |
| `knode attach <node> <file>` | Attaches a file to a node (v0.3) |
| `knode attachments <node>` | Lists the files attached to a node (v0.3) |
| `knode markdown folder <folder>` | Keeps a folder of Markdown files in step with the space, both ways from v0.3 (v0.2) |
| `knode markdown deleted` | Lists the notes whose file was deleted from the live Markdown folder, and keeps or deletes them (v0.3) |
| `knode sources <action>` | Lists what brings things into the space, importers and connectors, and connects, pauses, excludes chats from or disconnects a connector (v0.4). See [sources](#sources) |
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

A project folder holds the files of the project of the same name: adding `~/code/site` adds the
project `site` to the space, or uses the project named `site` that the space already has. Names are
compared without regard to upper and lower case, extra spaces or how accented letters are encoded.
`[`, `]` and `|` cannot be part of a title, so they become `(`, `)` and `-`: `site [old]` adds the
project `site (old)`. Its notes and the projects inside it are listed under it, in the app and by
the MCP `projects` tool. The folder's line in `knode folders list` names its project. If two devices
each add the folder before they sync, the space can end up with two projects of that name; the
folder then uses the oldest.

Two different folders with the same name are kept apart. A folder uses the project of its name only
when no other folder holds that project yet, such as one an import made. When another folder holds
it, the new folder gets a project of its own, named after its parent folder too: with `~/w/a/api`
holding `api`, adding `~/w/b/api` adds the project `api (b)`, and `knode folders add` says which
folder holds `api`. Adding a folder again keeps its project and says it already holds it. Each
folder remembers its project, so adding another folder, or renaming the project, never moves it.

Adding the same folder again changes it, and adds its project if the space has none, such as for a
folder added before Knode did this. Removing a folder keeps everything already captured from it, and
its project with the notes that are part of it. A folder inside your home folder is kept as `~/...`,
so the space finds it on your other Macs even when your user name differs there; a folder that is
not on this machine is kept and marked `missing here`.

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
  the start of a word. Case and accents are ignored. Titles, note text and imported Markdown
  paths are searched: `knode search --words DEPLOY.md` finds a note imported from
  `Projects/DEPLOY.md`, even if its title is “Release instructions”. Best for names, ids and
  exact terms. Existing spaces add paths to their search index when opened by this version.
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
- **Conversations, chats and mail** (captured agent conversations, imported chats and imported mail
  threads) are long and mention many things in passing. Ranking words and meaning together, they
  count half, so the short note that answers your question usually comes before a conversation that
  only mentions it. `--words` and `--meaning` rank them like any note.
- **Turning it off:** `knode index off`, or in `config.toml`:

  ```toml
  embeddings = "off"
  ```

  `knode index run` turns it on again. `KNODE_EMBEDDINGS=off` in the environment of Knode Service
  turns it off too.

`knode service status` shows the same as `knode index status`.

## import

```sh
knode import <path or URL> [--from <kind>] [--json]
knode import --from apple-notes
knode import --from bear
knode import --from readwise-api
knode import --from apple-mail [<path>] [--folder <name>] [--since <day>] [--until <day>] [--attachments]
knode import <file.mbox> [--folder <name>] [--since <day>] [--until <day>] [--attachments]
knode import <folder> --vault [--report <file.json>] [--include-deleted]
```

Brings what you already have into the space. Knode tells what a path is by its name and what is
inside it; `--from <kind>` says it instead, when a path could be more than one thing (a folder of
Markdown that Apple Notes exported, for example). It prints one line with how many nodes are new,
updated and unchanged, then the nodes it wrote, anything it skipped and why, and what it read only
in part. `--json` prints the same report as JSON.

Importing the same thing again does not make copies: Knode finds the nodes it wrote before and
changes only what changed. A note you edited in Knode since is never overwritten by an import of
the same, unchanged source.

### Preview a vault migration

```sh
knode import /path/to/vault --preview
knode import /path/to/vault --preview --json
```

No destination space is required. This runs the actual Markdown-folder importer in a private
temporary local space, reports its result and deletes that space. The source vault and any
configured destination remain unchanged. It does not start Knode Service, download an embedding
model, connect an agent or call an external API. Only Markdown folders are supported in preview.

The report lists imported titles/source paths, unresolved wikilinks, repeated lowercased titles,
skipped files with reasons, attached and unused files, and failed typed links. It also warns
about ignored hidden paths/symlinks, canvases not converted by Markdown import, uppercase `.MD`
files and possible plugin-dependent queries/templates. Plugin detection is heuristic, not an
exhaustive compatibility audit. Preview does not expose temporary node IDs as final IDs.

Review all unused files, especially shared attachment folders: current attachment import covers
files referenced by name next to a note and attachment mappings in Knode exports, not every
Obsidian layout. Do not treat source-text preservation as plugin execution support.

Keep the source stable during preview and import: this is not a filesystem snapshot. The
rehearsal uses ordinary temporary disk proportional to the imported data; deletion is not secure
erasure. It tests a new space, not merge/conflict behavior against an existing destination.
`simulation_only: true` in JSON makes this distinction explicit.

After reviewing the report, import a pilot into an explicitly selected new space, inspect notes,
links and attachments, and export for comparison before cutover. Exports can add Knode metadata;
this report is not proof of byte-identical Obsidian round-trip fidelity or ongoing synchronization.
Keep your original vault and a backup until those checks pass. The whole path, step by step, is
in [Moving from Obsidian](07-migrate-from-obsidian.md).

### Migration report

```sh
knode import /path/to/vault --vault --report report.json
```

From v0.3. For a folder of Markdown files, `--report <file.json>` writes the migration report:

- every file of the folder, hidden ones included, with its `status` (`created`, `updated`,
  `unchanged`, `edited`, `attached`, `skipped` or `failed`), the `node` id of each note and the
  `reason` for anything that was not simply imported (a hidden folder such as `.obsidian`, a
  canvas, a file no note uses, a note that is not UTF-8 text);
- `counts` of each status;
- `unresolved_links`: `[[links]]` that name no note;
- `ambiguous_links`: `[[links]]` that name several notes by title, with the one they point at
  (the nearest by folder, as Obsidian does) and every candidate;
- `missing_attachments`: files a note embeds or links to that are not attached, with any file of
  the folder that could be it (a shared attachment folder, for example) and why;
- `failed_links`: typed links of the frontmatter that could not be made;
- `limits`: what the report does not check.

The summary on screen lists the same problems, one line each, also without `--report` when there
are any. `--json` includes the report as `report`. The receipt of a guided import
(`onboarding.json`) maps each source path to its node in `import.nodes`.

### Importing a vault again

From v0.3, importing the same folder again into the same space finds each note by its path relative to that folder (or by
the `id` in its frontmatter) and compares it with what the last import wrote:

| The file since the last import | The note in Knode since the last import | What happens |
|---|---|---|
| unchanged | unchanged | `unchanged` |
| changed | unchanged | the note takes the file's changes: `updated` |
| unchanged | edited | your edit is kept: `unchanged` (the report says it was edited in Knode) |
| changed | edited | your edit is kept and the file's changes are **not** brought in: `edited`, for you to merge by hand |
| unchanged or changed | deleted | the note stays deleted: `skipped`, with the reason "deleted in Knode, not imported again" and the id of the deleted note |

A note you deleted in Knode is not imported again, whether or not its file changed: the summary
lists it and the report counts it in `counts.deleted`. `--include-deleted` imports such notes
again, as new notes. (Knode remembers the note of each file without an `id`, so it can tell a
note deleted in Knode from a new file.)

A note stays `edited` on every import until the two agree: copy what you want from the file into
the note (or the note into the file), and the next import is `unchanged` again. Notes imported by
an earlier version of Knode take the file once, as before; the earlier text stays in their history
(`knode history`, `knode restore`).

An import writes one note at a time. If it stops halfway (the computer sleeps, a file cannot be
read), run it again: notes already written are found and left alone, the rest are written, and
typed links, made at the end, are made then. Fixing a file that failed and importing again brings
in only that file. A file renamed or moved in the vault without an `id` in its frontmatter is
imported as a new note; the note of its old path stays, and you can delete it.

### Importing

Everything `knode import` reads, with the kind `--from` takes:

| Source | What to give it | `--from` |
|---|---|---|
| Claude, ChatGPT or Gemini (v0.3) | The data export `.zip`, or the folder it was extracted to | `export` |
| A folder of Markdown, or an Obsidian vault | The folder | `markdown` |
| A Markdown file | The `.md` file | `markdown` |
| Claude Code or Codex | A session file (`.jsonl`) | `session` |
| PDF (v0.3) | The `.pdf` file | `pdf` |
| Web pages (v0.3) | A saved page (`.html`), or an `http` or `https` URL | `web` |
| Kindle (v0.3) | `My Clippings.txt` from the device, or a notebook exported from a Kindle app (`.html`) | `kindle` |
| Readwise (v0.3) | The CSV export, or the Markdown export (a `.md` file or the unzipped folder) | `readwise` |
| Readwise API (v0.3) | Nothing: Knode reads your highlights from Readwise with your access token | `readwise-api` |
| Apple Notes (v0.3) | Nothing, to read the notes on this Mac, or a folder of notes exported as Markdown | `apple-notes` |
| Bear (v0.3) | Nothing, to read the notes on this Mac, or a backup (`.bear2bk`), TextBundles or an exported Markdown folder | `bear` |
| Notion (v0.3) | The "Markdown & CSV" export `.zip`, or the folder it was extracted to | `notion` |
| Evernote (v0.3) | An `.enex` file, or a folder of them | `evernote` |
| Gmail (v0.3) | The `.mbox` file of a Google Takeout export of Mail, or the folder it is in; any other `.mbox` file too | `mbox` |
| Apple Mail (v0.3) | Nothing, to read the mail on this Mac, or a mailbox exported from Apple Mail (the `<name>.mbox` folder) | `apple-mail` |
| WhatsApp (v0.3) | A chat export: the `.zip`, the folder it was extracted to, or the chat's `.txt` | `whatsapp` |
| Telegram (v0.3) | A Telegram Desktop export in JSON: the folder with `result.json`, a `.zip` of it, or the `result.json` | `telegram` |
| Slack (v0.3) | A workspace export: the `.zip`, or the folder it was extracted to | `slack` |

- **A folder of Markdown:** every `.md` file in it and in its subfolders, leaving out hidden ones
  such as `.git`, `.obsidian` and `.trash`. Each file becomes a note titled with its file name, or
  with the `title` of its frontmatter; its text is kept as it is, and it keeps the dates the file
  was created and last changed. A folder written by
  `knode export` brings back every note with its id, kind, tags, dates, authors, links and
  properties. An Obsidian vault keeps its frontmatter, tags and `[[links]]`, including links written
  as paths, such as `[[Projects/README]]`, which find the note imported from that file. A link to a
  name several notes share, such as `[[README]]` or `[[app/README]]`, finds the one nearest to the
  note that links, as Obsidian does. `--vault` only changes
  the wording of the summary. From v0.3, a file that is not Markdown, such as an image, is
  attached to the note that embeds it with `![[name]]` (or links to it with `[[name]]`) when it is
  in the same folder as the note; other such files are left out. A kind of link the space does not
  have yet is added to it, so no link is lost. See [Markdown in and out](#markdown-in-and-out).
- **A Markdown file:** one note, titled with the `title` of its frontmatter, else its first heading,
  else its file name. Its frontmatter is kept as properties. The note is known by where the file
  is: importing the same file again updates that note, while a file with the same name in another
  folder, or in a folder imported as a whole, is a note of its own. A note an earlier version of
  Knode imported from the file is updated too, not copied.
- **A session file:** one node of kind `conversation`, with its title, project and date, written
  as that agent.
- **A data export:** Knode tells Claude, ChatGPT and Google Takeout (Gemini Apps) apart by what is
  inside. Each conversation becomes a node of kind `conversation`, with its title, model and dates;
  Claude projects become notes, and their conversations link to them as `part-of`. A newer export
  of the same account updates the conversations instead of copying them.
- **A PDF:** one node of kind `source` with its text, page by page, its title and author, and the
  PDF attached. A PDF with no text, such as a scan, still becomes a node, and the report says so.
- **A web page:** one node of kind `source` with the readable article as Markdown, its canonical
  URL, author and site. Scripts on the page are never run. Knode only goes online when you give
  it an `http` or `https` URL, and then fetches that one page. A page that changed updates its
  node, unless you edited the node in Knode since: then the report says it was left as it is.
- **Kindle and Readwise:** one node of kind `source` per book or article, with each highlight, its
  note and its location. The same book from Kindle and from Readwise is one node, and importing
  again adds only the new highlights.
- **Readwise API:** the same books, articles and highlights as the Readwise exports, read straight
  from Readwise, so the API, the CSV and the Markdown export never make copies of each other. See
  [Readwise through its API](#readwise-through-its-api).
- **Apple Notes:** with no path, Knode reads the notes on this Mac, from the Notes database, only
  ever reading it. macOS lets only apps with Full Disk Access read it: if Knode cannot, it says
  how to turn it on in System Settings > Privacy & Security > Full Disk Access for the app that
  runs Knode (your terminal, or Knode), or you can export the notes and import the exported
  folder with `--from apple-notes`. Folders, checklists, links between notes, hashtags and
  attached files are kept; tables, drawings and scans are listed as read only in part. Locked
  notes and notes in Recently Deleted are left out.
- **Bear:** with no path, Knode reads Bear's database on this Mac, which needs Full Disk Access in
  the same way; a backup or TextBundles do not. Tags, nested tags, `[[links]]` and files are kept.
- **Notion:** pages and sub-pages become notes, linked `part-of` their parent page, and each row
  of a database becomes a note with its columns as properties.
- **Evernote:** each note keeps its notebook, tags, dates, attached files, checklists and tables.
  Encrypted text is left out.
- **Mail:** see [Mail](#mail).
- **Chats:** see [Chat exports](#chat-exports).

```sh
knode import ~/Downloads/data-2026-10-20.zip
knode import ~/Documents/"My Clippings.txt"
knode import https://example.com/essays/on-rivers
knode import --from apple-notes
knode import ~/Downloads/Export-notion.zip --json
```

A path that is none of these fails and says what `knode import` takes.

### Mail

```sh
knode import ~/Downloads/Takeout/Mail/"All mail Including Spam and Trash.mbox"
knode import ~/Downloads/Takeout/Mail --folder Work --since 2026-01-01
knode import --from apple-mail --folder INBOX --until 2026-06-30 --attachments
knode import --from apple-mail ~/Desktop/Trips.mbox
```

From v0.3. Knode never signs in to a mail account: it reads the files you point it at, and
writes only into your space. For Gmail, export your mail with
[Google Takeout](https://takeout.google.com) (choose Mail), unzip the download and import the
`.mbox` file in `Takeout/Mail`, or that folder. For Apple Mail, `--from apple-mail` with no path
reads the mail stored on this Mac, only ever reading it. macOS lets only apps with Full Disk
Access read it: if Knode cannot, it says how to turn it on in System Settings > Privacy &
Security > Full Disk Access, and for which program: the app that runs Knode (your terminal, or
Knode), or, when Knode Service runs the import, the `knode` program itself, by its path. Or
export a mailbox from Apple Mail (Mailbox > Export Mailbox) and import the exported `.mbox`
folder with `--from apple-mail <path>`; it needs no Full Disk Access.

A folder that holds other things besides mail, such as a vault with one `.mbox` file in it, is
imported as Markdown unless you name the kind with `--from mbox` or `--from apple-mail`.

- **One node per thread**, of kind `email`, titled with the subject. Replies are found by the
  headers mail programs write (`Message-ID`, `In-Reply-To`, `References`). A reply that has
  none of them joins the thread with the same subject only when it shares someone with the
  thread and was sent at most 30 days after its last message. When one message ties together two
  threads imported before, it goes to the older one and the two are linked; no message is ever in
  two threads. The messages are in the order they were sent, each with its sender, its
  recipients (`To`, `Cc`, and `Bcc` on a line of its own) and the time it was sent, in the
  sender's time with its offset from UTC (`2026-03-03 09:00 +01:00`). Knode keeps the plain text
  of a message and turns its HTML into Markdown when there is no plain text.
- **Remote images never load by themselves:** an image a message loads from the web, which can
  tell the sender you opened it, becomes a link (`[image: shop.example](...)`) that loads nothing
  until you follow it, and tracking pixels (1x1 or hidden images) are left out.
- **Very large messages:** a message larger than 64 MB is skipped, and the import says so.
- **People:** everyone who sent or received a message becomes one node of kind `person`, one per
  address, and each thread links to its people, with two limits so that a newsletter or a
  mailing list does not fill your space with people: the recipients of a message sent to more
  than 20 addresses do not become people (its sender does), and `Bcc` recipients become people
  only in mail in a sent folder: `Sent`, `Sent Mail`, `Sent Messages`, `Sent Items`, or the
  name mail programs give it in Spanish, Portuguese, Catalan, French, German, Italian, Dutch,
  Swedish, Danish or Norwegian (`Enviados`, `Envoyés`, `Gesendet`, `Inviati`, `Verzonden`...).
  Every address is still listed in the thread, and the import says how many were not added as
  people. A later import leaves a person node as it is,
  so a name or note you add is kept; only a node titled with a bare address gets the person's
  name once a message gives it.
- **Folders and dates:** `--folder` imports only the messages in a Gmail label or an Apple Mail
  mailbox (`--label` and `--mailbox` are other names for it); repeat it for more than one.
  `--since` and `--until` take a day (`2026-01-01`) in UTC, both included; a message with no
  date is left out when you give one. Spam and trash are left out unless you name them with
  `--folder`.
- **Attachments** are left out unless you add `--attachments`: then the files of each message are
  attached to its thread, up to 100 MB each. Files attached before stay linked when you import
  again without `--attachments`. A file the space cannot take, and the files Apple Mail keeps
  outside some messages, are named in the thread but not attached.
- **Importing again** adds only the new messages to their threads. Importing another date range
  later adds those messages to the threads already there. A message in two folders (Gmail's All
  Mail and your Inbox in Apple Mail, for example) is imported once.
- **Your edits are kept:** a message you changed in Knode is left as you wrote it, and text you
  add between messages or after the last one stays where you put it. Knode only writes a message
  again while it is as Knode wrote it.

### Chat exports

Each chat becomes one node of kind `chat`, titled with the app and the chat's name, such as
`WhatsApp - Book Club`. Its messages are grouped by day under `## 2026-03-04` headings, each with
who sent it and when:

```markdown
## 2026-03-04

**Ada** (09:15): Shall we read the lighthouse novel next?

**Grace** (09:16): Yes!
It is long though.

**Ada** (09:17): ![[00000003-PHOTO-2026-03-04-09-17-00.jpg]]

*(09:20) Ada added Linus*
```

- **Files:** photos, voice notes, videos and documents in the export are attached to the chat's
  node, where the message shows them. A file the chat names but the export does not have, or an
  export made without media, is marked in the text (`*[missing from the export: ...]*`), and the
  summary counts them.
- **Importing again:** the same chat is found again, and only the messages after the last one
  imported are added, under the day they belong to. Importing the same export twice changes
  nothing; a newer export of the chat adds what is new. Messages older than the last one
  imported are not added; when an export has more of them than were imported, the summary says
  how many were left out.
- **Properties:** `chat.app`, `chat.id`, `chat.type` (`personal`, `group`, `channel`,
  `private-channel` or `thread`),
  `chat.participants`, `chat.started_at`, `chat.ended_at` and `chat.messages`. Times are as the
  export writes them, in the time of the phone or computer that made it.
- **Told from the path:** without `--from`, a path is a chat export only when the export is the
  path itself: its files at the top, or in the one folder at the top (of a folder or a `.zip`).
  A folder that holds Markdown files anywhere, outside the export's own media folders, is a
  folder of notes and is imported as Markdown, so no note is left out. When it also holds a chat
  export, the summary says where and gives the `knode import --from whatsapp`, `--from telegram`
  or `--from slack` command that imports it. Named with `--from`, an export is found at any depth.
- **Code:** code blocks keep their lines as written, blank lines included, and start on the line
  after the sender.
- **Privacy:** chats hold other people's messages. Knode reads only the files you give it,
  writes only into the space you import into, and sends nothing anywhere. What is shaped like a
  credential in the messages is replaced with `[redacted]`, and the summary says how many were:
  API keys and tokens in known formats, a long value of letters and digits after a name such as
  `password:` or `token=`, and the password in a link. Ordinary words are kept, so "Secret:
  everything went fine" stays as written.

**WhatsApp:** on the phone, open the chat, then Export Chat (with or without media). Give
`knode import` the `.zip`, the folder it was extracted to, or the `.txt` (`WhatsApp Chat with
<name>.txt` from Android). The date and time formats WhatsApp writes in each language are read:
day or month first, 12 or 24 hours, two or four digit years, with brackets (iPhone) or a dash
(Android), and with a word before the time where the language writes one (`04/03/2026 à 09:15`
in French, `4.3.2026 klo 9.15` in Finnish).

- **Day or month first:** WhatsApp does not say which. A day over 12 tells; otherwise the order
  that keeps the messages in time; otherwise the habit of the format (`3/4/26, 9:15 AM` is month
  first, as in the United States, and other formats day first). When nothing in the dates tells,
  the summary says how they were read. The order is kept on the chat's node
  (`chat.date_order`), and later exports of the chat are read the same way. If a later export
  shows the order was wrong (a date such as `3/14/26`), nothing is added and the summary says so:
  delete the chat's node and import the new export again.
- **The chat's name:** a WhatsApp export has no id for the chat, so the chat is found again by its
  name, from the export's file names: `WhatsApp Chat with <name>.txt` on Android, and for an
  iPhone's `_chat.txt`, the `.zip` (`WhatsApp Chat - <name>.zip`) or the folder it is in. Keep the
  names WhatsApp gave them. A loose `_chat.txt` in a folder WhatsApp did not name is refused,
  since Knode cannot tell which chat it is.

```sh
knode import ~/Downloads/"WhatsApp Chat - Book Club.zip"
knode import --from whatsapp ~/Downloads/"WhatsApp Chat with Ada.txt"
```

**Telegram:** in Telegram Desktop, export one chat (the chat's menu, Export chat history) or the
whole account (Settings > Advanced > Export Telegram data), choosing **Machine-readable JSON** as
the format. Give `knode import` the folder with `result.json`. An account export brings in every
chat in it, the ones you left included. Bold, italic, struck-out text, code, links and quotes stay
as Markdown; a reply says who it answers, when, and how that message starts. Photos, files and
voice messages are attached when the export includes them; the export settings decide which media
it downloads, and those left out are marked. Each chat is found again by its Telegram id, so a
renamed chat stays the same node. An account export is read one chat at a time, so it can be
any size; one chat can be up to 512 MB of `result.json`.

```sh
knode import ~/Downloads/Telegram\ Desktop/ChatExport_2026-03-06
knode import --from telegram ~/Downloads/Telegram\ Desktop/DataExport_2026-03-06
```

**Slack:** a workspace owner or admin exports the workspace from Workspace settings >
Import/Export Data > Export, and gets a `.zip`. Give `knode import` the `.zip` or the folder it
was extracted to. Each channel, private channel, group message and direct message in it becomes
a node (`Slack - general`). A thread becomes its own node, `Slack - general - <how it starts>
(<date and time>)`, linked `part-of` its channel, and later replies are added to the thread's
node. When the thread already had replies the first time its first message was imported, that
message also says `Thread: [[...]]` in the channel. Replies whose first message is not in the
export are kept in their thread's node too, which says that its start is not in the export. The members who wrote or
were mentioned become nodes of kind `person`, found again by their Slack id (`slack.user`), so
importing again never copies them; a mention becomes a `[[link]]` to the person, a channel name
a link to the channel's node, and every chat is linked `related` to the people who wrote in it.
Bold, italic, struck-out text, code and links become Markdown. A Slack export holds only links to
files, which need a Slack sign-in to open, so they stay links (without the access token Slack
puts in a file's private link); files an export includes (in
`__uploads/`, as some export tools write them) are attached. Times are shown in your computer's
time zone.

```sh
knode import ~/Downloads/"Example Slack export Mar 1 2026 - Mar 6 2026.zip"
```

### Readwise through its API

```sh
knode readwise token set
knode import --from readwise-api
knode readwise token delete
```

Get your access token at [readwise.io/access_token](https://readwise.io/access_token).
`knode readwise token set` reads it from standard input and keeps it in the same place as agent
keys: the Keychain on macOS. You can also set the `READWISE_TOKEN` environment variable, which
is used first. The token is only ever sent to Readwise: it is never written to a space, to the
history or to the logs.

`knode import --from readwise-api` reads every highlight the first time, then only what changed
in Readwise since the last import into that space: the space remembers when it last synced, not
the token. Highlights you discarded in Readwise are left out. When Readwise asks Knode to slow
down, Knode waits as long as Readwise says and tries again.

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
knode export <folder> --compare <vault> [--json]
```

From v0.2. Writes every node of the space to `<folder>` as a `.md` file, creating the folder if
needed, and prints how many files it wrote, left as they were, and removed. Exporting again to the
same folder rewrites only what changed. See [Markdown in and out](#markdown-in-and-out).

From v0.3, `--compare <vault>` then compares the export with the folder you imported, and changes
neither. For each note and file of the vault it says whether the export has it `identical`, with
only frontmatter changes (usually just the `id` Knode adds, listed by key), `changed` (with the
first line of text that differs), `moved` (the same text at another path, such as a note renamed
in Knode) or `missing` (such as an image in a shared attachment folder that was never attached). It
also lists the files only in the export (notes made in Knode) and what import never reads, such as
`.obsidian/` and `.trash/`, so nothing is silently counted as lost or as kept. `--json` prints all
of it. It compares text and files, not Obsidian itself: plugins, settings and how a note renders
are not checked. The export folder must be apart from the vault: Knode refuses to export into it,
into a folder inside it or into a folder that holds it.

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
  decide (see [markdown deleted](#markdown-deleted));
- `[[Title]]` links in the files become links between notes.

When a note changed in Knode and in the folder since the file was last written, each field keeps
the side that changed it. A body changed on both sides is merged when the note is being edited
together (see [Editing together](04-mac-app.md#editing-together-v03)); otherwise the side changed last wins and the other
version stays in the history.

Each space has its own folder. Knode warns you when the folder is inside iCloud Drive, Dropbox,
OneDrive or Google Drive, because two programs syncing the same files can make conflicted copies; a
space syncs on its own.

## markdown deleted

```sh
knode markdown deleted [--keep <node> | --delete <node>]
```

From v0.3. Lists the notes whose file was deleted from the live Markdown folder, with the path the
file had. They wait for you to decide:

- `knode markdown deleted --keep <node>` keeps a note, by id or title: its file is written to the
  folder again.
- `knode markdown deleted --delete <node>` deletes the note in Knode too, as you. Its history keeps
  it, so `knode restore` can bring it back.

Use `all` as `<node>` to keep or delete every note listed. The Mac app asks the same with a banner
(see [Mac app](04-mac-app.md#files-deleted-from-the-markdown-folder-v03)).

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

## sources

> Not released yet: arrives with v0.4. The connectors themselves (Apple Mail, Messages,
> WhatsApp, Telegram, Gmail, Slack) come one by one; `knode sources list` shows the ones your
> version has.

A *source* is anything that brings things into a space. *Importers* run once on what you give
them, with [`knode import`](#import). *Connectors* keep running in Knode Service and bring in
what is new, the same nodes an export of the same chats would give. Every connector is off until
you connect it to a space.

```sh
knode sources list
knode sources connect whatsapp
knode sources connect telegram --history everything
knode sources containers whatsapp
knode sources exclude whatsapp 34600000000-1600000000@g.us --name "Family"
knode sources include whatsapp 34600000000-1600000000@g.us
knode sources pause whatsapp
knode sources resume whatsapp
knode sources run whatsapp
knode sources disconnect whatsapp
```

| Action | What it does |
|---|---|
| `list` | Lists the connectors, each with its state, the history chosen, how much it brought in, when it last ran and its last error, then the importers. `--json` prints it as JSON |
| `connect <source>` | Connects a connector to the space and turns it on. It brings in what happens from today on, or with `--history everything` everything it has. The choice is kept: to change it, disconnect and connect again. Older history can still come from an export |
| `pause <source>`, `resume <source>` | Stops the connector until you resume it |
| `containers <source>` | Lists the chats, channels or folders the connector has now, with the excluded ones marked |
| `exclude <source> <id>` | Excludes a chat, channel or folder: it is never read again. `--name` gives the name shown in the list. What it already brought in stays |
| `include <source> <id>` | Reads an excluded chat, channel or folder again from now on |
| `run <source>` | Runs the connector now instead of waiting for its next run |
| `disconnect <source>` | Disconnects the connector. What it brought in stays in the space |

Everything comes in once a connector is connected, new chats included, except what you exclude.
Spam and trash stay out. The space keeps your choices (on or off, the history, what is excluded),
so they are the same on every device. What a connector did on this machine, where it stopped and
its last error, is kept in your Knode home and holds nothing it read.

Knode Service runs each connector when the files it reads change, or every few minutes for one
that reads a service over the network, and after a restart picks up where it stopped without
writing anything twice. After an error it waits longer each time, up to an hour. Connectors that
read another app's database (Mail, Messages, WhatsApp for Mac) read a copy of it, never the app's
own file, and never change it; if the app changed how it stores things, the connector stops,
writes nothing and `knode sources list` says it needs an update. Some connectors need a
permission, such as Full Disk Access on macOS, or a sign-in; the list says which. Passwords and
sign-in sessions stay in the system keychain, never in a space, a settings file or a log.

To stop every connector on this machine, add to `config.toml`:

```toml
sources = false
```

`capture = false` stops connectors too: Knode Service then brings nothing in by itself.

### Apple Mail

On macOS, the `apple-mail` connector brings in new mail from Apple Mail on your Mac. Every
account you added to Mail comes along: Gmail, iCloud, Outlook, Exchange, any other IMAP
account, and the mailboxes On My Mac. For Gmail this is the way that needs no setup: add the
account to Mail and connect the connector.

```sh
knode sources connect apple-mail
knode sources containers apple-mail
knode sources exclude apple-mail "imap://4A1B2C3D-0000-4000-8000-00000000A001/Newsletters" --name "Newsletters"
```

- **Full Disk Access.** macOS lets only apps with Full Disk Access read your mail. Turn it on for
  Knode in System Settings > Privacy & Security > Full Disk Access, then quit and reopen Knode;
  until then `knode sources list` shows the connector's error saying so.
- **When.** New mail is in your space within a minute of Mail receiving it.
- **What it reads.** Mail's files in `~/Library/Mail`, without changing them: a copy of Mail's
  index of messages, made when it changed, read and then deleted, and the message files it
  names. If a new version of macOS changed how Mail stores things, the connector writes nothing
  and says it needs an update.
- **Every mailbox comes in**, new ones too, except spam and trash (`Junk`, `Spam`, `Trash`,
  `Bin`, `Deleted Messages`, `Deleted Items`) and the mailboxes you exclude. `containers` lists
  each mailbox by its name, followed by its account when two accounts have one with the same
  name, and its id, the one `exclude` takes. Messages Mail marked as deleted are left out.
- **The same nodes as importing mail**: one node per thread, of kind `email`, the people of each
  thread, and what you wrote in a thread kept when new messages come in. A message you already
  imported from an Apple Mail export or a Gmail Takeout `.mbox` is found by its `Message-ID` and
  not added twice, and Gmail mail that Mail shows in several mailboxes (Inbox and All Mail)
  comes in once.
- **Attachments** are named in the thread, not attached. To attach them, import the mailbox
  with `knode import --attachments`.
- A message Mail has listed but not downloaded yet comes in on a later run, once Mail has it.

### Messages (iMessage)

On macOS, the `imessage` connector brings in the iMessage and SMS conversations of Messages on
your Mac.

```sh
knode sources connect imessage
knode sources containers imessage
knode sources exclude imessage "iMessage;-;+15550100" --name "Ada"
```

- **Full Disk Access.** macOS lets only apps with Full Disk Access read your messages. Turn it on
  for Knode in System Settings > Privacy & Security > Full Disk Access; until then
  `knode sources list` shows the connector's error asking for it.
- **What it reads.** A copy of the Messages database, made each time the connector runs, read and
  then deleted. Messages' own database is never opened or changed. If a new version of macOS
  changed how Messages stores things, the connector writes nothing and says it needs an update.
- **One node per conversation**, of kind `chat`, the same as a [chat import](#import): the
  messages grouped by day under `## YYYY-MM-DD`, each as `**Sender** (HH:MM): text`, with
  `chat.app` set to `imessage` and `chat.id` to the conversation's id in Messages (the one
  `exclude` takes). Group conversations keep their name; a new name given in Messages shows as a
  line of its own.
- **People** show as their phone number or email address, and you as `Me`.
- **Files** sent in a conversation are attached to its node when they are on the Mac; one that is
  not (kept only in iCloud, or deleted) is named in the text as not attached.
- **Reactions** (tapbacks) are left out. An edited message shows its last text, marked
  *(edited)*; one unsent shows *[unsent]*. A reply names the message it answers.
- **Later edits are not applied.** A message edited or unsent in Messages after the connector
  brought it in keeps, in Knode, the text it had then; edit the note by hand if it matters.
- If Messages numbers its messages again, as after its database is rebuilt or moved to a new
  Mac, the connector notices, reads everything once more and adds to each conversation only
  what it is missing.

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
| `list` | Lists the agents of this machine, whether their key is valid, and what each may do in each space, then the agents whose conversations are captured, with the folder each is read from, or that capture is off |
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
| `status` | Shows its version, process id, open spaces, connected clients and their last-used spaces, the default space conversations are captured into, how far search by meaning has indexed the spaces, and whether it starts at login |
| `install` | Starts it at login, and now: a launch agent on macOS, a systemd user unit on Linux |
| `uninstall` | Stops starting it at login, and stops it |

For each authenticated connection that has used a space, `status` lists its connection id,
person (app/CLI) or agent identity, and the last space it successfully used. Switching spaces
updates that entry; disconnecting removes it. Refused calls do not change it. This inventory is
only available to you, not to agents, and connection ids reset when the service restarts.

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

These are the agents whose conversations are captured, and where each keeps them. `knode agents
list` shows the ones on this machine.

| Agent | Sessions read from |
|---|---|
| Claude Code | `~/.claude/projects` (or `$CLAUDE_CONFIG_DIR/projects`) |
| Codex | `~/.codex/sessions` (or `$CODEX_HOME/sessions`) |
| Gemini CLI | `~/.gemini/tmp` (or `$GEMINI_CLI_HOME/.gemini/tmp`) |
| GitHub Copilot CLI | `~/.copilot/session-state` (or `$COPILOT_HOME/session-state`) |
| OpenCode | `opencode.db` in `~/.local/share/opencode` (or `$XDG_DATA_HOME/opencode`) |
| Amp | `~/.local/share/amp/threads` (or `$XDG_DATA_HOME/amp/threads`) |
| Aider | `.aider.chat.history.md` in each project, searched for under the folder named by `KNODE_AIDER_ROOT`, up to four folders down |
| Cursor | Chats and agent sessions in Cursor's `User/globalStorage/state.vscdb` (or `$KNODE_CURSOR_ROOT`) |
| Windsurf | Chats kept in Windsurf's `User/workspaceStorage` (or `$KNODE_WINDSURF_ROOT`). Cascade conversations are not captured: see below |
| Cline | The extension's `tasks` folder in VS Code, VS Code Insiders, VSCodium, Cursor or Windsurf, and `tasks` and `sessions` in Cline's data folder, `~/.cline/data` (or `$CLINE_DATA_DIR`, or `$CLINE_DIR/data`), where the Cline CLI and Cline's SDK keep them (or `$KNODE_CLINE_ROOT` instead of all of these) |
| Roo Code | The extension's `tasks` folder in VS Code, VS Code Insiders, VSCodium, Cursor or Windsurf (or `$KNODE_ROO_CODE_ROOT`) |

An editor's data folder is `~/Library/Application Support/<editor>` on macOS and
`~/.config/<editor>` (or `$XDG_CONFIG_HOME/<editor>`) on Linux.

Capture runs in Knode Service. When it starts at login, through launchd on macOS or a systemd user
unit on Linux (`knode service install`), it does not see variables you set only in a shell
profile, such as `CLINE_DATA_DIR`, `CLINE_DIR`, `XDG_CONFIG_HOME` or the `KNODE_*_ROOT` variables,
and keeps reading the default folders. Set them where the service can see them too, with
`launchctl setenv` on macOS or in `~/.config/environment.d/` on Linux, then restart it with
`knode service stop` and `knode service start`.

Windsurf keeps Cascade conversations in `~/.codeium/windsurf/cascade` as encrypted files whose
format is not public, and has no way to export them, so Knode cannot read them. Connect Windsurf
with `knode connect windsurf` instead: Cascade then reads and writes your memory through MCP.

Each conversation is one node, with its agent, project, branch, model and the dates it started
and ended, when the agent records them. Aider writes every session of a project to the same file,
and each session becomes its own node. Subagents' side conversations are left out where the agent
marks them, and so are tool output and the model's reasoning.

## instructions

`knode instructions codex|claude-code --project <folder> [--apply] [--remove]` previews, installs
or removes a versioned project-local memory workflow. It preserves other rules and saves numbered
backups on changes. Without `--apply` it writes nothing. It does not connect an agent or change
spaces, permissions or capture. `knode connect <agent> --instructions <folder>` combines connection
with installation; `--print` previews both. After installing, it says when the agent reads the file and which other files it reads with it;
start a new agent session to load it. See [When the agent picks up a change](06-project-workflow.md#when-the-agent-picks-up-a-change)
and [Which instructions win](06-project-workflow.md#which-instructions-win).
