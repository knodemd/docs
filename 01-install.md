# 1 - Install

Knode comes as one command, `knode`, and as a Mac app. The command is enough to give every agent a
shared memory; the app lets you see and organise it.

> Not released yet: these commands work from v0.1.

## The command line

The `knode` command holds the CLI, the MCP server for your agents and Knode Service, the process
that keeps your spaces open.

| System | Install |
|---|---|
| macOS | `brew install knodemd/tap/knode` |
| Linux (from v0.2) | `brew install knodemd/tap/knode` or `curl -fsSL https://knode.md/install.sh \| sh` |

The install script puts `knode` in `~/.local/bin` after checking the download against the release's
checksums, and never asks for root. If that directory is not in your `PATH`, it tells you the line to
add. To pick a version or another directory:

```sh
curl -fsSL https://knode.md/install.sh | KNODE_VERSION=0.2.0 KNODE_INSTALL_DIR=~/bin sh
```

On Linux there are two builds. From v0.3 the script picks the static musl build on systems that use
musl, such as Alpine, and on glibc older than 2.35, and the glibc build everywhere else. To choose
one yourself, set `KNODE_LIBC` to `gnu` or `musl`:

```sh
curl -fsSL https://knode.md/install.sh | KNODE_LIBC=musl sh
```

Check it works:

```sh
knode --version
```

### Knode Service

Knode Service is the one process that opens your spaces, so you, the app and every agent can write
at the same time and see each change at once. You do not need to start it: any `knode` command that
uses a space, and the MCP server your agents run, start it in the background when it is not
running. To have it running from the moment you log in, which also keeps capturing your agent
conversations while no agent is open:

```sh
knode service install
```

On macOS this adds a launch agent; on Linux, a systemd user unit. See
[the command line](02-cli.md#service) for the other `knode service` commands.

## The Mac app

```sh
brew install --cask knodemd/tap/knode
```

Or download it from [knode.md](https://knode.md). The app carries the `knode` command inside and
offers to install it the first time you open it; you can also do it later from **Knode > Install
Command Line Tool…**. It adds `knode` to `/usr/local/bin`, or to `/opt/homebrew/bin` when only that
folder is writable, and asks for your password when your Mac needs it. If `knode` is already
installed, for example with Homebrew, the app uses that one, says so and installs nothing.

## Connect your agents

```sh
knode connect claude-code
```

To see every agent Knode can be added to, and which of them are on this machine:

```sh
knode connect --list
```

| Agent | Command |
|---|---|
| Claude Code | `knode connect claude-code` |
| Codex | `knode connect codex` |
| Gemini CLI | `knode connect gemini` |
| Claude Desktop | `knode connect claude-desktop` |
| Cursor | `knode connect cursor` |
| Windsurf | `knode connect windsurf` |
| Cline (in VS Code, VS Code Insiders, VSCodium, Cursor or Windsurf, and Cline CLI) | `knode connect cline` |
| Roo Code (in the same editors) | `knode connect roo-code` |
| GitHub Copilot CLI | `knode connect copilot-cli` |
| GitHub Copilot in VS Code | `knode connect vscode` |
| OpenCode | `knode connect opencode` |
| Amp | `knode connect amp` |
| Zed | `knode connect zed` |
| Aider | No MCP support yet; `knode connect aider` prints what you can do instead |

Add `--print` to see what would change first. [Agents and MCP](03-mcp.md#where-each-agent-is-configured)
lists the file each one changes.

Or add the MCP server by hand, once the agent has a key from `knode connect <agent>`. For example,
in Claude Code:

```sh
claude mcp add knode -- knode mcp --agent claude-code
```

## Later

| What | Where |
|---|---|
| iPhone and iPad app | App Store, from v0.4 |
| Web app | [app.knode.md](https://app.knode.md), from v0.5 |
| Rust libraries | `cargo add knode-format` and `cargo add knode-md` |
| Swift package | Add `https://github.com/knodemd/knode` in Xcode |

## Requirements

macOS 26 or later on Apple silicon or Intel. Linux on x86_64 or arm64 from v0.2, with glibc 2.35 or
later (for example Ubuntu 22.04, Debian 12, Fedora 36 or newer). From v0.3, any other Linux on
x86_64 or arm64 too, including musl distributions such as Alpine and older glibc, through the static
musl build that the install script picks for them. Homebrew on Linux always installs the glibc
build.

## Uninstall

If you ran `knode service install`, undo it first:

```sh
knode service uninstall
```

Then:

```sh
brew uninstall knode
brew uninstall --cask knode
```

If you installed it with the install script, delete the file: `rm ~/.local/bin/knode`.

If the Mac app installed the command, choose **Knode > Uninstall Command Line Tool…** before you
delete the app. It removes only the `knode` link the app made.

Your spaces are `.knode` files and stay where you saved them.
