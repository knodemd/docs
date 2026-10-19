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

Check it works:

```sh
knode --version
```

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
knode connect codex
knode connect gemini
knode connect claude-desktop
```

Or add the MCP server by hand, for example in Claude Code:

```sh
claude mcp add knode -- knode mcp
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
later (for example Ubuntu 22.04, Debian 12, Fedora 36 or newer). Distributions built on musl, such as
Alpine, are not supported yet.

## Uninstall

```sh
brew uninstall knode
brew uninstall --cask knode
```

If you installed it with the install script, delete the file: `rm ~/.local/bin/knode`.

If the Mac app installed the command, choose **Knode > Uninstall Command Line Tool…** before you
delete the app. It removes only the `knode` link the app made.

Your spaces are `.knode` files and stay where you saved them.
