# 4 - Mac app

The Mac app shows your spaces and lets you read, write and organise them.

> Not released yet: a first version arrives with v0.1.

## In v0.1

- Open a `.knode` space, or create a new one, from the File menu or the welcome screen.
- Reopen a space you used recently: File > Open Recent, or the list on the welcome screen.
- List its notes, the most recently changed first.
- Read and edit a note in Markdown, with a live preview. Changes are saved as you type, and each
  one is recorded as made by you, with the name of your Mac user account.
- Search titles and text.
- Try the example space to see how it works. Its notes stay in memory and are never saved.
- See what your agents write as they write it, through Knode Service.

## Several spaces (v0.2)

Keep work and personal memory apart in different spaces. The sidebar lists every space you created
or opened, like libraries in Apple Music. The first time, it shows your default space, the other
spaces Knode knows, the spaces you opened recently and the spaces in `~/Knode`.

Every space you create or open in the app is added to the spaces Knode knows (`spaces` in
`config.toml`), so Knode Service captures conversations into it by its folders (see below).

- **New space:** File > New Space… (Shift-Command-N), or **New Space…** at the bottom of the
  sidebar. Give it a name and choose where it goes (`~/Knode` by default). Turn on **Use for agents
  and capture** to make it the space your agents and captured conversations write to.
- **Switch:** click a space, or press Command-1 to Command-9 for the first nine.
- **Rename:** Space > Rename…, or right-click the space. This changes the name Knode shows; the
  `.knode` file keeps its name. A rename or a new icon made elsewhere, such as with
  `knode spaces rename`, shows in the sidebar at once while Knode Service runs.
- **Icon:** Space > Choose Icon… and pick one emoji. The icon is stored in the space, so it follows
  the space to another Mac.
- **Default space for agents:** the space marked with a sparkle is the one the `knode` command, your
  agents and capture use when none is named (`default_space` in `config.toml`). Change it with
  **Use for Agents and Capture** in the Space menu or the right-click menu.
- **Remove from list:** takes the space off the sidebar. Its files stay where they are; open it
  again with File > Open Space… (Command-O). Knode asks whether to also **stop capturing into it**:
  only then is it taken out of the spaces Knode knows (and out of the default, if it was), so
  capture no longer writes to it.

### Folders and capture

Each space keeps the folders that feed it. Choose **Folders and Capture…** in the Space menu or the
right-click menu of a space, or open Settings > Spaces. There you can:

- turn **capture into this space** on or off: off, nothing captured is written to it;
- **add a folder** with the **Add Folder** menu: a **project** (a folder you and your agents work
  in, such as a code repository), a **notes** folder of Markdown files, or an **agent** folder
  where an agent keeps its sessions (choose the agent from the list);
- turn **capture off for one folder**, or remove it. Removing a folder never removes what was
  already captured from it.

Each folder shows its kind and its path, and **Not on this Mac** when it does not exist here: it
is kept, and used again on a Mac where it exists.

How a captured conversation finds its space: a conversation an agent holds in a project folder
goes to every space that lists the folder with capture on; one held anywhere else goes to your
default space. Turn capture off for a project and nothing of it is kept, in any space. The same
settings are in the terminal as `knode folders` and `knode spaces capture` (see the
[CLI](02-cli.md#folders)).

## History of a note (v0.2)

Every change to a note is kept, with who made it. Press Command-Y, or click the clock in the
toolbar, to see the history of the open note beside it: every version, newest first, with the
person or agent that made it (and the agent's session), when, and how many lines it added and
removed.

Click a version to compare it with the note as it is now, line by line (**Changes**) or in two
columns (**Side by Side**). From there:

- **Restore This Version** puts the whole note back as it was. The restore is a new version, so you
  can always go back again.
- **Undo This Change** undoes only what that one change did. If the note changed again after it,
  for example an agent wrote to it later, Knode says so and asks before undoing it anyway, because
  that would also undo the later changes. To go back past them on purpose, restore a version
  instead.

Edits you have not saved yet are saved before a restore or an undo. Press Escape or **Done** to go
back to the note.

### Undo your last change

While you type, Command-Z and Shift-Command-Z undo and redo your typing, as in any text editor.
To undo a whole saved change, even one you made yesterday, use the history from the keyboard:

- **Edit > Undo Last Change to Note** (Option-Command-Z) undoes the most recent change **you** made
  to the open note: an edit or a restore. Press it again to go further back.
- **Edit > Redo Last Change to Note** (Shift-Option-Command-Z) puts back what you just undid. Once
  you edit the note again, there is nothing left to redo.

Changes made by your agents are never undone this way: undo them from the history. If someone else,
such as an agent, changed the note after your change, Knode asks before undoing it, as above. Each
undo and redo is a new version in the history, so nothing is lost.

## Agents and permissions (v0.2)

Each agent you connect has its own key and sees only the spaces you give it. Open
**Knode > Settings… > Agents** (Command-Comma) to see every agent on this Mac:

- its name and kind, such as `claude-code`, and when it was added;
- its key: **Active**, with the start of its fingerprint, or **Revoked**. The key itself stays in
  your Keychain and is never shown;
- for each of your spaces, what it may do there:
  - **No Access**: the space is invisible to it, not just empty.
  - **Read**: it can read and search the space, but not change it.
  - **Read and Write**: it can also write, and undo its own changes.

A change takes effect on the agent's next call. **Revoke…** stops every key of the agent at once and
takes away its access to every space. Its past changes keep its name. Connect it again to give it a
new key.

To change who can see one space, choose **Agent Access…** in the Space menu, or right-click the
space.

**Connect an Agent…** adds Knode to Claude Code, Codex, Gemini CLI or Claude Desktop, the same as
`knode connect <agent>` in the terminal, and shows what the command printed. **Show What It Will
Do** only describes the changes first. The first time, a new agent can read and write only your
default space.

Agents are managed through Knode Service. If it is not running, the screen offers to start it.

> Permissions are not a sandbox. They decide what an agent can do through Knode. A program running
> as you can still open the files of a space directly, or run the `knode` command, which acts for
> you.

## The map: graph and canvases (v0.3)

### Graph

Press Shift-Command-M, or click the graph button in the toolbar, to see the whole space as a graph:
every note is a point and every link a line between two. Press it again to go back to your notes.

- Each kind of note has its own mark: notes, conversations, decisions, ideas, sources and canvases.
  The legend in the corner says which is which.
- Faint lines are links written in the text with `[[double brackets]]`; dashed lines are typed
  links, such as *part of* or *answers*. Point at a note to see how each of its links reads, or at a
  line to see its kind.
- Drag the background, or scroll, to move around. Pinch, scroll while holding Command, or use the
  zoom buttons to zoom. **Fit** shows everything.
- Drag a note to move it; the others make room. Click a note to open it.
- **Find a note:** type part of a title and press Return to centre it and mark it.
- **Filter:** show only some kinds of note, kinds of link, tags, people or agents, or notes changed
  today, in the past 7 or 30 days, or in the past year.

**Saved views.** The **Whole Space** menu saves what you see as a named view: its filters, the
part of the graph on screen and where every note is. Open it again from the same menu and the graph
looks exactly as you left it, on any Mac that opens the space. Moving notes in a saved view keeps
their new places. From the same menu you can save new filters to the view, rename it or delete it.

### Canvases

A canvas is a board where you arrange notes by hand. Choose **File > New Canvas** (Option-Command-N);
it appears in the list like a note, and opens as a board.

- **Add notes:** drag them from the list onto the board, or click **Add Notes** and pick them.
- **Move and resize:** drag a note; drag its corner to resize it. Shift-click to select several and
  move them together. Double-click a note to open it.
- **Show only the title:** right-click a note and choose **Show Title Only**.
- **Groups:** click **New Group** to draw a labelled box, around the selected notes if any. Notes
  dropped inside a group join it and move with it. Click the arrow on a group to fold it, and
  double-click its label to rename it.
- **Link notes:** right-click a note, choose **Link To**, the other note and how they relate.
- **Remove from canvas:** right-click a note. The note stays in your space.
- Zoom and move around as in the graph.

Everything is saved as you go. Moving things around is not a change to your notes, so it never
fills their history; while the canvas is open, Command-Z and Shift-Command-Z undo and redo your
moves, additions and removals.

## Links and backlinks (v0.3)

Press Shift-Command-L, or click the link button in the toolbar, to see the links of the open note
beside it:

- **Links:** the notes this one links to, grouped by how they read from here, such as *mentions* or
  *part of*.
- **Backlinks:** the notes that link here, read the other way, such as *mentioned in* or *has part*.

Click a note to open it. Right-click a typed link to remove it; links written in the text go away
when you remove the `[[link]]` from the text.

**Add Link…** links the open note to another and says how they relate: *related to*, *part of*,
*derived from*, *answers* or *contradicts*. Choose **New Kind** to name a relation of your own: how
it reads from this note (*depends on*), how it reads from the other one (*needed by*), or that it
reads the same both ways, and what it means, for you and your agents.

## Knode Service and the menu bar

When Knode Service is running, the app opens your spaces through it, like your agents do, so a note
an agent writes appears in the list as it is written, and the note you are reading updates when an
agent changes it (unless you are editing it). When the service is not running, the app opens the
space itself and keeps working; it moves back to the service when the service starts. Renaming a
space, its icon, capture and folders go through the service too while it runs, so your agents and
capture see each change at once.

The Knode icon in the menu bar shows the service. It is dimmed while the service is stopped. Its
menu shows:

- whether Knode Service is running, the spaces it has open and how many apps and agents are
  connected;
- **Open Knode**, to bring up the window;
- **Start Knode Service** or **Stop Knode Service**;
- **Run at Login**, to start the service every time you log in (the same as
  `knode service install`);
- **Agents and Access…**, to open the permissions screen;
- **Quit Knode**.

Closing the window keeps Knode in the menu bar; quit it from the menu.

## The knode command

The app carries the `knode` command inside. The first time you open it, it offers to install the
command so your terminal and your agents can use it; you can also install or uninstall it from the
Knode menu. If `knode` is already installed with Homebrew, the app uses that one. See
[Install](01-install.md#the-mac-app).

## Next

See the [roadmap](https://knode.md/plan) for the rest.
