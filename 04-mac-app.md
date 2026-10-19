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
or opened, like libraries in Apple Music. The first time, it shows your default space, the spaces
you opened recently and the spaces in `~/Knode`.

- **New space:** File > New Space… (Shift-Command-N), or **New Space…** at the bottom of the
  sidebar. Give it a name and choose where it goes (`~/Knode` by default). Turn on **Use for agents
  and capture** to make it the space your agents and captured conversations write to.
- **Switch:** click a space, or press Command-1 to Command-9 for the first nine.
- **Rename:** Space > Rename…, or right-click the space. This changes the name Knode shows; the
  `.knode` file keeps its name.
- **Icon:** Space > Choose Icon… and pick one emoji. The icon is stored in the space, so it follows
  the space to another Mac.
- **Default space for agents:** the space marked with a sparkle is the one the `knode` command, your
  agents and capture use when none is named (`default_space` in `config.toml`). Change it with
  **Use for Agents and Capture** in the Space menu or the right-click menu.
- **Remove from list:** takes the space off the sidebar. Its files stay where they are; open it
  again with File > Open Space… (Command-O).

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

## Knode Service and the menu bar

When Knode Service is running, the app opens your spaces through it, like your agents do, so a note
an agent writes appears in the list as it is written, and the note you are reading updates when an
agent changes it (unless you are editing it). When the service is not running, the app opens the
space itself and keeps working; it moves back to the service when the service starts.

The Knode icon in the menu bar shows the service. It is dimmed while the service is stopped. Its
menu shows:

- whether Knode Service is running, the spaces it has open and how many apps and agents are
  connected;
- **Open Knode**, to bring up the window;
- **Start Knode Service** or **Stop Knode Service**;
- **Run at Login**, to start the service every time you log in (the same as
  `knode service install`);
- **Quit Knode**.

Closing the window keeps Knode in the menu bar; quit it from the menu.

## The knode command

The app carries the `knode` command inside. The first time you open it, it offers to install the
command so your terminal and your agents can use it; you can also install or uninstall it from the
Knode menu. If `knode` is already installed with Homebrew, the app uses that one. See
[Install](01-install.md#the-mac-app).

## Next

- Permissions per agent and per space (v0.2).
- A map of your nodes and links (v0.3).

See the [roadmap](https://knode.md/plan) for the rest.
