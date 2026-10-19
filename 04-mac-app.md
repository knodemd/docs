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

- Several spaces, switched like Apple Music libraries (v0.2).
- History of every change, and who made it (v0.2).
- A map of your nodes and links (v0.3).

See the [roadmap](https://knode.md/plan) for the rest.
