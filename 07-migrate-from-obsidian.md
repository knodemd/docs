# 7 - Moving from Obsidian

How to move an Obsidian vault (or any folder of Markdown notes) to Knode in steps you can undo at
every point. Knode never changes the vault: every step below reads it, and the import writes only
to the space you name. The commands are in [Command line](02-cli.md#import).

What this guide does not promise: that exporting the space gives back your vault byte for byte
(that is tracked in [knode#20](https://github.com/knodemd/knode/issues/20)), or that Obsidian and
Knode stay in step on their own (see [Ongoing sync](#ongoing-sync-with-obsidian) below).

## 1. Back up

Before anything else, make a copy of the vault that nothing will touch, for example:

```sh
cp -R ~/Vault ~/Vault-backup-2026-11-17
```

Include the hidden folders (`.obsidian/`, `.trash/`): Knode does not import them, so the backup is
the only place their settings, plugins and deleted notes are kept. If you already use Knode, back
up the space you will import into too: `knode export ~/Knode-backup` writes every note to a folder,
and copying the `.knode` file while Knode is closed keeps everything else.

## 2. Preview

```sh
knode import ~/Vault --preview
```

This runs the real importer into a temporary space, prints what it would do and deletes the space.
Look at what it says before going on:

- notes whose `[[links]]` find no note, and titles several notes share;
- files that are skipped or not attached, such as images in a shared `attachments/` folder;
- warnings about canvases, plugin queries (Dataview, Tasks, Templater) and hidden paths.

Fix in the vault what you want fixed (or accept it), and preview again.

## 3. Import a pilot

Import into a **new** space first, not the one your agents already use:

```sh
knode init vault-pilot
knode import ~/Vault --vault --space vault-pilot --report ~/vault-report.json
```

Always import the same folder into a space. Knode finds a note again by its path relative to the
folder you import, so `Atlas/Plan.md` imported from `~/Vault/Projects` and `Projects/Atlas/Plan.md`
imported from `~/Vault` are two different notes: importing a subfolder and then the whole vault
into the same space brings that subfolder's notes in twice.

To pilot one project rather than the whole vault, import only its folder into a **throwaway**
space, and delete that space once you have looked at it:

```sh
knode init atlas-trial
knode import ~/Vault/Projects/Atlas --vault --space atlas-trial
```

Its links to notes outside the folder show as unresolved: that is expected for a trial. The space
you keep (`vault-pilot` above, or the one you will use) gets the whole vault, imported from
`~/Vault` from the start.

`vault-report.json` is the migration report: the node each file became, what was created, updated,
unchanged, attached, skipped or failed and why, links that find no note or several, and embedded
files that were not attached. Keep it: it is the map from your vault to the space. See
[Migration report](02-cli.md#migration-report).

## 4. Validate

Check a few notes by hand, then let Knode compare everything.

**Representative notes.** Pick a handful the report shows as `created`: a plain note, one with
frontmatter and tags, one with many `[[links]]`, one in a deep folder, one that embeds an image or
a PDF, one with the same title as another (the report's `ambiguous_links`). For each:

```sh
knode search "a phrase from the note" --space vault-pilot
knode links "Note title" --space vault-pilot        # its links, out and in
knode attachments "Note title" --space vault-pilot  # the files it embeds
```

Open them in the Mac app too: the text, tags and links should be what you wrote.

**Every note: export and compare.**

```sh
knode export ~/vault-pilot-export --space vault-pilot --compare ~/Vault
```

Export to a folder of its own: Knode refuses to export into the vault, into a folder inside it, or
into a folder that holds it.

The comparison lists, for every note and file of the vault, whether the export has it identical,
with only frontmatter changes, changed, moved or missing, plus what is only in the export and what
was never imported. What to expect after a clean import:

- every note with **only frontmatter changes**, the key being `id` (Knode adds the note's id as the
  first line of the frontmatter). The text after the frontmatter is the same;
- images and other files next to the note that embeds them **identical**;
- files in shared attachment folders, canvases and files no note uses **missing**: they were not
  imported (the report says why);
- `.obsidian/`, `.trash/` and other hidden paths **not imported**: listed, never counted as lost.

Anything **changed** or **moved** that you did not do in Knode deserves a look. `--json` gives the
whole comparison for scripts or an agent to check.

## 5. Choose the source of truth

After the pilot, decide which one you edit from now on. Do not edit both: there is no automatic
sync between them.

- **Knode is the source of truth (recommended).** Import the whole vault into the space you will
  use, with `--report` again, then stop editing the vault. Keep it read-only as an archive until
  you are confident. If you want to keep reading your notes in Obsidian or another editor, turn on
  the [live Markdown folder](02-cli.md#markdown-folder) to a **new** folder (not the vault): Knode
  writes every note there and reads your edits back, with the space as the source of truth.
- **Obsidian stays the source of truth for now.** Keep editing the vault and import it again when
  you want Knode up to date. Edits you make in Knode are not written to the vault, and an import
  does not overwrite them: a note changed in both places since the last import is reported as
  `edited` and left as it is in Knode, for you to merge by hand. See
  [Importing a vault again](02-cli.md#importing-a-vault-again).

## 6. Switch, reversibly

When the pilot passes:

1. Import the whole vault, from the same folder as the pilot (`~/Vault`), into the space you will
   use, with `--report`. Or keep the pilot space, if it has the whole vault, and make it the
   default with `knode spaces default vault-pilot`. Never import into the space you keep a
   subfolder you tried on its own: its notes would come in twice (see step 3).
2. Compare again with `knode export <folder> --compare ~/Vault`.
3. Point your agents at that space (see [Install](01-install.md)).
4. Keep the vault backup from step 1 untouched for as long as you like.

To go back at any point:

- before switching: delete the pilot space (it is one `.knode` file); the vault was never changed;
- after switching: `knode export ~/Vault-from-Knode` writes every note, with your edits in Knode,
  as Markdown Obsidian opens; your backup still has the vault as it was, settings included.

If an import stops halfway, run the same command again, on the same folder and into the same
space: notes already written are found by their path in that folder (or the `id` in their
frontmatter) and left alone, and the rest are added, so none of them comes in twice. Notes
renamed or moved in the vault since, without an `id`, are the exception: they come in as new
notes (see below).

## Ongoing sync with Obsidian

Knode does not keep an Obsidian vault and a space in step today:

- importing again brings in changes made in the vault, but never deletes a note whose file was
  deleted, and a file renamed without an `id` in its frontmatter comes in as a new note;
- a note you deleted in Knode stays deleted when you import the vault again: the import says it
  was not imported again, and `--include-deleted` brings such notes back as new notes;
- edits made in Knode stay in Knode until you export them;
- Obsidian plugins, settings, themes, workspaces and canvases are not migrated, and plugin queries
  (Dataview and the like) are kept as text, not run;
- the live Markdown folder syncs a space with a folder both ways, but pointing it at your existing
  vault makes Knode write its own frontmatter into your notes: point it at a new folder.

A plugin that keeps a vault and a space in step both ways is planned after v1.0
([knode#64](https://github.com/knodemd/knode/issues/64)).
