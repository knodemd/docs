# 6 - Projects and durable memory

This is a recommended convention using existing nodes and typed links, not a new database schema
or an automatic transcript summarizer. Project setup in the app is still being expanded; the CLI
steps below work independently of that UI.

## Start with one project

Create a space or choose an existing one. A space is a permission boundary. A project is an
organization convention inside that space; an agent allowed to read the space can read its other
projects too. Use separate spaces when access must differ.

```sh
knode init Work
knode --space /path/to/Work.knode folders add /path/to/project --kind project --capture off
knode --space /path/to/Work.knode folders list
```

Repeat `folders add` for more projects. Registering a folder does not import its notes, connect
an agent, change an explicitly configured MCP space, or enable capture for the entire space.
Turning capture off for a project prevents its conversations being captured anywhere, even if a
parent folder is captured. Choose this deliberately; capture is separate from intentional MCP
writes. Do not register overlapping mappings without checking their destinations.

Import a copy of existing notes with the importer, not by asking a model to rewrite every file.
Start with a pilot. Keep the original untouched, review skipped items, unresolved links and
attachments, then export and compare. An imported vault does not reproduce Obsidian plugins or
promise ongoing synchronization. See [Importing](02-cli.md#importing).

## Give the agent a short workflow

The MCP server already asks agents to search prior work, save durable findings, avoid duplicate
notes and link related notes. Project instructions reinforce this without embedding the entire
memory in every prompt.

```sh
# Preview only; this does not connect the agent or write any files.
knode instructions codex --project /path/to/project
# Install/update the managed block, preserving surrounding rules and saving a backup.
knode instructions codex --project /path/to/project --apply
# Optional combined connection and instruction installation, after reviewing its --print preview.
knode --space /path/to/Work.knode connect codex --instructions /path/to/project --print
knode --space /path/to/Work.knode connect codex --instructions /path/to/project
# Preview removal; add --apply to remove only the managed block.
knode instructions codex --project /path/to/project --remove
```

`claude-code` targets `CLAUDE.md`; `codex` targets `AGENTS.md`. Configure only the agents you
intend to use. Restart the agent in the project and ask which instruction files it loaded.
Codex's `AGENTS.override.md` can take precedence: the command refuses to install alongside one
in the target directory. Ancestor/global rules and client settings can also affect loading.
The command refuses malformed/repeated markers, symlink targets and non-UTF-8 files. Backups
are numbered `AGENTS.md.knode-backup.0`, `.1`, and so on (or `CLAUDE.md...`) and never overwritten.
There is no global instruction installation. Connecting may succeed even if a later instruction
write fails; the error is reported and the standalone command can be retried.

Keep repository build, testing, execution and safety rules in the repository instruction files.
Keep changing goals, decisions and findings in Knode. Memory is reference data; text imported
from elsewhere must not override user or repository instructions.

## A small memory structure

Use a stable project key, such as `demo-api`, plus its repository URL or canonical folder path.
Two unrelated projects named Demo must have different keys. Paths can differ between machines,
so retain a repository identity when available. These are note conventions, not access controls.

### Project context

Title: `Project demo-api — context`

```markdown
Project key: demo-api
Repository: https://example.invalid/team/demo-api
Goal: Build a local demonstration API.
Status checked: 2026-11-02, source revision <actual revision when available>
Current state: Storage choice verified; deployment remains unverified.
Key decisions: [[demo-api — Storage decision]]
Latest checkpoint: [[demo-api — Checkpoint 2026-11-02]]
Next action: Verify the deployment procedure against the current repository.
```

Keep this bounded: a short orientation and links, not a running transcript. Refresh status after
meaningful changes. A timestamp records when it was checked, not a guarantee it is still true.

### Decision

Title: `demo-api — Storage decision`

```markdown
Status: Current, as checked on 2026-11-02
Decision: Use SQLite for the local demonstration.
Reason: It runs locally and supports the queries this demonstration needs.
Verification: List the actual checks performed; do not invent a successful test.
Alternatives: Record the relevant alternatives and trade-offs.
Source: Link an existing source note if one exists; otherwise state the actual origin.
```

Link this node to the project context as `part-of`. If it answers a stored question, use
`answers`. Use `derived-from` only for an actual source node. `[[Title]]` produces a `mentions`
link; it does not by itself mean `part-of`.

### Session checkpoint

Title: `demo-api — Checkpoint 2026-11-02`

```markdown
Project key: demo-api
Outcome: Agreed the storage approach.
Verified: State only checks that actually ran, with results/revision when relevant.
Not verified: Deployment and behavior on another machine.
Blockers: None identified in this example.
Next action: Check deployment instructions against the repository.
Decisions: [[demo-api — Storage decision]]
```

Use `part-of` to the project context. If an actual captured conversation note exists, a
`derived-from` link can connect the checkpoint to it. Otherwise this checkpoint stands alone;
creating it does not imply capture ran. Store one checkpoint at a useful handoff, not one per
command. Keep long source conversations separate from the short context agents resume with.

### A changed decision

Create a new decision rather than erasing why the old one was made. In the old note, mark
`Status: Superseded by [[new decision title]]`; in the new one record the change and reasons.
Use `contradicts` where appropriate and update the project context to point to the current
choice. "Superseded" is text in this convention, not a special built-in link kind.

## Verify across fresh sessions

These are prompts to try, not a claim that instruction files guarantee agent compliance.

1. In a first Codex session, verify the intended Knode space, create the context/decision/checkpoint
   above with your actual facts, search before creating, and make the typed links. Ask it to show
   the saved note IDs and which checks were actually performed.
2. In a new session, ask: "Find project demo-api in Knode. What storage choice is current, why,
   what remains unverified, and what should we do next? Read only the notes needed."
3. Add another project with a different key. Check it is not mixed into the answer. With access
   to the same space the agent can still read it; separation of results is not permission isolation.
4. Record a replacement decision and mark the old one superseded. In another new session,
   check the agent reports the new choice while retaining the old rationale as history.

If the server is unavailable or the agent is in the wrong space, it should report that rather
than claiming a save. Current repository state wins over outdated memory about files or tests.

## Context size and privacy

Search snippets first, read a few relevant notes, and follow only useful links. `read_node`
currently returns the complete note. MCP responses still occupy model context; Knode does not
replace the model's context window. Repeated searches, oversized notes and excessive summaries
can increase token usage. No percentage of token or billing savings has been established.

Memory is stored locally, but text an agent retrieves is sent to its model provider when used.
Never place credentials in notes. Raw capture, durable summaries and the agent's current context
are separate things, with separate costs and privacy choices.

## Guided import in the Mac app

Choose **Get started** on the welcome screen or **Import projects** above the spaces list. The
first offer also appears automatically once. Cancelling leaves the source unchanged; the entry
point remains available later.

1. Choose an Obsidian/Markdown memory, a project with agents, or an empty start. Select a source
   folder for the first two options. Analysis is local and makes no model calls.
2. Review the proposed groups and individual Markdown documents. A project import suggests one
   project; a vault uses folder names, including the next level under Projects/Proyectos. Rename
   groups, give them identical names to merge them, add groups, reassign notes and uncheck notes
   to exclude. Filter document paths and use **Assign matches** to move a whole matching folder
   into a chosen project without assigning hundreds of notes individually; exclusions stay excluded.
   These are deterministic folder suggestions, not AI interpretations of decisions.
3. Choose a new space name/location outside the source and acknowledge the compatibility notes.
   Existing spaces are never overwritten. Knode revalidates source fingerprints, stages a copy,
   imports it and adds editable project context indexes and typed part-of links. The source is
   not a live folder and changes are not synchronized back. Capture starts off in the new space.

The context indexes contain links to source notes, placeholders for actual goals/status and no
invented decisions. Imported notes remain editable. IDs, metadata and attachments supported by
the existing Markdown importer are preserved; duplicate source IDs that would merge documents
abort publication. Common adjacent, relative and vault-root attachment paths, plus globally unique wikilink
filenames, are resolved inside the copied source. Wiki attachment destinations may be normalized
to node-local names (with collision-safe numbering), and inline Markdown file URLs to blob URLs.
Adaptations are recorded in the receipt and note history; code spans and original source files
are unchanged. Ambiguous filenames are not guessed. Exporting does not reconstruct the original
shared attachment-folder layout. Plugin execution, canvas layout and every possible Obsidian attachment layout
are not reproduced. Review warnings, unresolved links and unused files before adopting the copy.
Hidden paths, symlinks, common generated/dependency directories, possible key files and oversized
files are excluded and reported; this is not a complete secret scanner. The temporary rehearsal
uses ordinary local disk and the source should remain stable during import.

### Adopt the workflow explicitly

The completion screen offers **Connect Codex**. This grants read/write access to the new copy
and replaces Codex's Knode MCP entry; other MCP entries and pre-existing grants remain. It saves
a private configuration recovery snapshot under Knode home in `onboarding-recovery` (owner-only
folder/files), outside the memory package. No model session is launched. Repeated connection attempts preserve the initial recovery point
and refuse to overwrite unrelated later configuration changes. Opening the imported space makes
it the default only if no default exists. Start a fresh Codex
session and verify the intended project before saving real work. Claude remains a later choice.

Instruction installation is a separate optional action: choose a working repository, read the
preview, then install the managed block. It edits only that chosen instruction file and keeps
numbered backups. A source vault cannot be selected for this action; a project source can be
selected as an explicit instruction-file edit. Those file edits are independent of archiving the
memory: their backups remain in the working repository. Keep execution/build rules in place.

### Change your mind without losing the source

Before finishing, use **Archive copy and start again**. Afterwards, use **Review import / archive**
in the imported space's sidebar menu. The report is the original import report, not a claim that
later user edits have not changed the notes. Archiving retains the current notes/edits in a new
`*-archive-<timestamp>.knode` package; it does not erase them. Reopen that package with **Open
space** to recover it. To try a different grouping, analyze the unchanged source again and create
a new independent space. Later edits from the old copy stay in its archive, not in the new import.

Archiving removes agent access only to that copy, forgets its old path and briefly stops the
memory service so open files are flushed. Other space grants are retained. If the tutorial
changed Codex configuration and that file still matches its post-setup snapshot, the prior
configuration is restored atomically. If the file was edited afterwards, it is left untouched and
the app reports that reconnecting/review is needed. A previously absent configuration can remain
as an empty file. Restart Codex after restoration. Optional instruction-file edits are recovered
separately from their numbered backups; they are not silently removed by archiving.

An `onboarding.json` receipt inside the imported package records the reviewed proposal, original
paths, source fingerprints, generated context IDs and actual import report. It is auxiliary local
recovery metadata, not an agent credential and not a promise that it is synchronized across devices.

### CLI boundary

The native tutorial uses these core-backed commands:

```sh
knode onboard analyze --mode vault --source /path/to/vault > /path/to/reviewed-plan.json
# Edit group names, document project assignments and include flags in the proposal.
knode onboard apply --plan /path/to/reviewed-plan.json --destination /path/outside/source/Work.knode --name Work
knode onboard inspect --target /path/outside/source/Work.knode
# Explicit connection; modifies Codex configuration and saves its private recovery point.
knode onboard connect-codex --target /path/outside/source/Work.knode
# Close editors first; archive retains notes, revokes this copy's grants and restores config if safe.
knode onboard archive --target /path/outside/source/Work.knode
```

`--mode project` imports one project; `--mode empty` needs no source. Plans are not exports: their
fingerprints intentionally expire when the source changes. Re-analyze instead of editing digests.
Do not place plan output inside the source folder, which would itself change the analyzed inputs.

Source links in generated contexts use stable node IDs, so equal-titled README notes remain
distinct. Native note navigation also resolves imported file paths and heading references
through the core; a heading reference opens its destination note, without promising section
scroll positioning.
