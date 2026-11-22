# 4 - Mac app

The Mac app shows your spaces and lets you read, write and organise them.

> Not released yet: a first version arrives with v0.1.

## In v0.1

- Open a `.knode` space, or create a new one, from the File menu or the welcome screen.
- Reopen a space you used recently: File > Open Recent, or the list on the welcome screen.
- List its notes, the most recently changed first. A note imported from a Markdown file shows
  the folder it came from next to its title, so many notes named `README` stay apart.
- Read and edit a note in Markdown, with a live preview. Changes are saved as you type, and each
  one is recorded as made by you, with the name of your Mac user account.
- Search titles and text.
- Try the example space to see how it works. Its notes stay in memory and are never saved.
- See what your agents write as they write it, through Knode Service.

## Projects

When a space has projects, such as those an import makes from your folders, the sidebar switches
between **Projects** and **Recent**. Projects lists the top-level projects with how many notes each
has; open one to see the projects and notes inside it, and **‹** to go back up. The main area shows
the project's page: what it is part of, its description, the projects inside it and its most recent
notes. **Edit description** opens the project itself as a note. Recent lists the most recently
changed notes: up to 1,000 while Knode Service runs, and up to 10,000 when the app opens the space
itself; the count ends in `+` when there are more. The graph can show only the projects (see
[Graph](#graph)).

The preview shows GitHub tables, and an HTML image line, such as a README banner, as a link to the
image: Knode does not download it.

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

- turn **capture into this space** on or off: off, nothing captured is written to it; turned on,
  it keeps conversations from then on, not earlier ones. A new space captures nothing until it is
  set up or you write something in it (see [Capture](02-cli.md#capture));
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

### Add project folders when creating a space

A project folder is where a project's files live, such as a code repository. The New Space sheet
offers **Add project folders next**: leave it on and **Folders and Capture** opens once the space
is created, or turn it off and add them later from the Space menu or Settings > Spaces.

Choose **Add Folder > Project folder**, pick the folder, then confirm it. Adding it also adds the
project of the same name to the space, such as `site` for a folder named `site`, or uses the project
of that name the space already has, compared without regard to upper and lower case. `[`, `]` and
`|` become `(`, `)` and `-`, and the confirmation shows the name it will use. If another folder of
the same name already holds that project, the new folder gets its own, named after its parent
folder too, such as `api (b)`, and the Folders screen says which folder holds `api`. The sidebar lists it
under **Projects**, and the folder's row names it. Removing the folder later keeps the project and
its notes, kept for that folder: another folder of the same name gets a project of its own, and
adding the folder again gives it back its project. A project no folder has held, such as one an
import made, goes to the first folder of its name. A folder added before Knode did this keeps the
project of its own name. **Capture
conversations held in this folder** is on, as for every folder; turn it off and nothing agents say
in that folder is captured, in any space. Capture also needs to be on for the space and for Knode
Service. Agents can still save notes through MCP when they can write to the space. Cancel and
nothing is added. Repeat for each project.

One space can hold several projects, and every agent that can read the space can read all of
them: use separate spaces when agents need different access. Adding a project folder does not
move or import its notes, connect an agent or change the space an agent uses. Import notes with
`knode import`, and connect agents in Settings > Agents.

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

### Connect your agents

The first time you open Knode, it opens **Get started** on **Let your agent do it**, which starts
with **Connect your agents**. If Get started is not open when the `knode` command is installed on
the first run (or found already installed), the app shows Connect your agents on its own. It is also
in **Knode > Connect Your Agents…**, whenever no other sheet is open; the menu item never replaces a
sheet you are using.

It lists the agents on your Mac, and any agent already connected: Claude Code first, then Codex,
then the others. Choose the space they get (your default space, unless you pick another), then
**Connect** one, or **Connect all detected**. Agents already connected show **Connected**. Once an
agent is connected, the app says what to do next: open a new session of that agent, and it will set
up your memory. If an agent could not be connected, for example because its own command is not
installed, the app shows what `knode connect` said.

The app runs `knode connect` with the `PATH` of your login shell and the variables that say where
agents keep their settings (`CLAUDE_CONFIG_DIR`, `CODEX_HOME`, `COPILOT_HOME`, `CLINE_DIR`,
`CLINE_MCP_SETTINGS_PATH`, `XDG_CONFIG_HOME`), so it configures the same folders your terminal
sessions use. If Knode runs from its disk image, Downloads or a temporary folder, it does not
connect agents, which would then run a copy that goes away: move Knode to Applications first (the
step offers **Move to Applications**). If your `knode` command is too old to list agents, the step
says to update it: `brew upgrade knodemd/tap/knode`.

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
- **Projects:** show only the projects, the projects they are part of and the projects related to
  them. Click it again to show every note.

**Saved views.** The **Whole Space** menu saves what you see as a named view: its filters, the
part of the graph on screen and where every note is. Open it again from the same menu and the graph
looks exactly as you left it, on any Mac that opens the space. Moving notes in a saved view keeps
their new places. From the same menu you can save new filters to the view, rename it or delete it.

### Watch what an agent reads

While an agent reads through Knode Service, the notes it opens or finds light up in the graph and
fade after a few seconds, and a line at the bottom says which agent is reading what. Turn on
**Follow** (top right) to move the view to each note it reads. Only you see this, never other
agents, and nothing of it is kept; what agents call is in the access log.

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

## Editing together (v0.3)

While Knode Service runs, you and your agents can write in the same note at the same time:

- What an agent writes appears in the open note as it writes it, while you keep typing. Your cursor
  and your selection stay with the words around them.
- What you type is merged with what the agents write, so nobody overwrites anybody. Knode sends
  your typing a moment after you pause, and at least once a second while you keep going.
- Under the title, **Claude Code is editing** (or the name of whichever agent or person it is) shows
  for a few seconds after someone else changes the note.
- Each merged edit is a change in the history of the note, with who made it.

When an agent's edit arrives, Command-Z starts over from there: to take back something you wrote
before, use **Edit > Undo Last Change to Note**. Without Knode Service, the app saves the whole note
as you type, as before, and the last save wins.

## Attachments (v0.3)

Keep images, PDFs and other files inside the space, with the note they belong to:

- **Drop files in the text** of a note to attach them; each is written as `![[name]]` where you
  dropped it. Drop them anywhere else on the note and they go where it ends.
- **Paste an image**, such as a screenshot, into the text. It is attached as a PNG named after when
  you pasted it, such as *Pasted image 2026-10-20 at 09.30.00.png*.
- **Drop files on a canvas** to add a note there with each file attached.
- Press Shift-Command-A, or click the paperclip in the toolbar, to see the files of the open note.
  **Attach Files…** picks files to attach. Right-click a file to open it, show it in the Finder,
  copy its `![[name]]`, or remove it.

In the preview, `![[name]]` on a line of its own shows an image inline, and a PDF as its first page
with **Open**; other files show their icon, name, size and **Open**. Clicking `[[name]]` opens the
file. A file can be up to 100 MB; a folder is not attached, attach the files in it instead.
Removing an attachment is a change you can undo: the file stays in the space. While Knode Service
runs, attachments go through it, so your agents see them at once.

## Search by meaning (v0.3)

The search in the toolbar finds notes by the words they contain and by what they mean, so it also
finds a note about "what we charge" when you search for *pricing*. The menu next to the search
picks how:

- **Words and Meaning**, the default: both, ranked together.
- **Words**: only notes with the words you type.
- **Meaning**: notes about what you type, even in other words or another language.

Each result shows the text around the words found, in bold, or the passage nearest in meaning, and
how it was found: **Words 1** is the first note by words, **Meaning 2** the second by meaning. The
app remembers the mode you chose.

Search by meaning runs on your Mac, in Knode Service, with a small model; nothing is sent anywhere.
The first time, Knode downloads the model once (about 490 MB), then indexes each open space in the
background, and waits while your Mac runs on battery. Until a space is ready, the search runs by
words and says why above the results, such as how many notes are left to index. Without Knode
Service, the app searches by words and says so.

**Settings > Search** shows whether search by meaning is on, the model and its download, and how
many notes of each open space are indexed and how many wait. **Search by Meaning** turns it on or
off (`embeddings` in `config.toml`, the same as `knode index off`); off, nothing is downloaded or
indexed and search is by words. **Index Now** indexes every open space at once, even on battery,
and tries a failed download again. The menu bar menu shows the same, with the same switch and
**Index Now**.

## Files deleted from the Markdown folder (v0.3)

When a file is deleted from a space's live Markdown folder (see
[markdown folder](02-cli.md#markdown-folder)), Knode never deletes its note. The note is kept,
marked, and no longer written to the folder until you decide. The list of notes then shows a calm
banner, such as **2 notes were deleted from the Markdown folder**. Click **Review** to see each
note with the path its file had, and choose, for one note or for all of them:

- **Delete** (or **Delete All in Knode**) deletes the note in Knode too, as you. Its history keeps
  it: **Undo** right after brings it back, and so does restoring it later.
- **Keep** (or **Keep All**) keeps the note, and its file is written to the folder again within a
  few seconds.
- **Later** (or **Decide Later**) leaves the note as it is. The banner stops asking about it until
  you open the space again.

The same choices are in the terminal as `knode markdown deleted` (see the
[CLI](02-cli.md#markdown-deleted)).

## Meetings and voice memos (v0.4)

Knode can record a meeting, write down everything that was said and turn it into minutes, all on
your Mac. Nothing is sent anywhere.

### Record a meeting

In the menu bar menu:

- **Record Meeting…** for a call (Meet, Zoom, Teams and others): it records the sound of your Mac,
  everyone else on the call, and your microphone, you.
- **Record In Person…** for people in a room: it records only the microphone.

Choose the space and the project the meeting belongs to, and start. While Knode records, the menu
bar shows a red dot and the time; **Stop** ends it. Nothing is ever recorded unless you start it,
for that meeting. The first time, Knode reminds you that some places require everyone's consent
before a meeting is recorded, and it gives you a short notice to paste in the meeting chat.

The first recording asks for access to the microphone and, for calls, to the sound of other apps;
allow both in System Settings > Privacy & Security if you said no.

### What you get

When the recording stops, a note of kind `meeting` appears in the project, with the audio attached:

- **The transcript**, a note of its own linked to the meeting: everything that was said, with the
  time of each part, and who said it in a call (**Me** and **Others**). It is always kept, so your
  agents can read the whole meeting and nothing is lost.
- **The minutes**, written into the meeting note: a summary, the key points, every decision, the
  action items with who owns them, and the open questions. They are written by Apple's on-device
  model, which needs Apple Intelligence turned on (System Settings > Apple Intelligence & Siri).
  Without it you still get the transcript, and the meeting says why there are no minutes.

Minutes are on for every space; turn **Write minutes** off for a space where you choose the space
and project of a recording, and its recordings get only the transcript. Transcribing and writing
the minutes go on in the background while the app is open; if you quit in the middle, they go on
the next time you open it. If a step fails, **Retry** appears in the menu bar menu.

A recording is never thrown away. If it cannot be stored in its space (Knode Service is stopped,
or the disk is full), it waits under **Unsaved recording** in the menu bar menu and Knode tries
again on its own; you can also save it again, show it in the Finder or delete it. Quitting Knode
while it records stops the recording and stores it first. After about three hours Knode warns you,
and a little later it stores the meeting and offers to record the rest as a new one.

If your microphone changes during a meeting, for example when you put in your AirPods, Knode goes
on recording from the new one.

### Voice memos

Connect **Voice Memos** in Settings > Sources: every memo you record, on your iPhone or your Mac,
comes into the space as a note of kind `voice-memo` with its audio, once iCloud has brought it to
this Mac, and is transcribed and summarised the same way. Choose whether to bring in only new memos
or the ones you already have, and leave out folders you do not want. Voice Memos needs Full Disk
Access for Knode; a memo still in iCloud is waited for.

## Calendar (v0.4)

Knode for Mac can bring the events of your calendars into a space, so you and your agents can
connect notes and meeting recordings to the meeting they came from. Everything stays on your
Mac: Knode reads your calendars, never changes them, and sends nothing anywhere.

### Connect your calendars

1. Open **Settings > Sources** and choose the space.
2. Under **Calendar**, click **Connect**.
3. Click **Allow Access**. macOS asks whether Knode may read your calendars: allow full access.
   Knode asks only now, when you connect the source.
4. Choose the history: **From today** (the default) or **Everything**.
5. Switch off the calendars you want to leave out, then click **Start**.

If you declined access, the source shows **Needs your attention**. Allow Knode in **System
Settings > Privacy & Security > Calendars**, then connect again or click **Check Again**.

### What comes in

- One note of kind `event` per event, titled with the event and its day, such as
  `Weekly sync (2026-11-24)`. Every occurrence of a recurring event gets its own note.
- The note says when and where it happens, the link to the call (Zoom, Google Meet, Teams and
  others), who is invited, the calendar, and the event's notes (cut when they are long).
- Attendees are named, never written with their mail address, unless you turn on **Attendees'
  addresses**. An attendee whose name matches a person note in the space links to it.
- When an event changes, its note changes with it. A cancelled event is marked **Cancelled**, and
  an event deleted from your calendar is marked **Removed from the calendar**; neither note is
  deleted.
- Events are written by Knode itself, as the `calendar` source.

### Which days

By default Knode keeps events from 30 days back to 14 days ahead. With **From today**, it never
goes back before the day you connected the source. Change **Days back** and **Days ahead** in the
Calendar card in **Settings > Sources**. Knode reads your calendars again when one changes, every
15 minutes, and when you click **Run now**.

### Meetings and events

When you start **Record Meeting…** during an event, the sheet offers **Link to <event>**. Leave it
on, and the meeting takes the event's title and is linked to the event in your space. If you
choose a project, the event becomes part of it too.

### Leave a calendar out, pause or disconnect

- **Choose calendars** in the Calendar card switches calendars on or off. A calendar switched off
  is never read again; what came from it stays.
- **On in <space>** pauses the source.
- **Disconnect** stops it. The events it brought in stay in the space.

## Photos, recordings and videos (v0.4)

Add the photo of a whiteboard, a voice note from a walk or a talk filmed on your phone to a space,
and Knode turns it into text your agents can search, on your Mac. Nothing is uploaded.

### Add them

In the Knode app for Mac, open a space, then:

- drag photos, audio files or videos onto the list of notes (or a folder: Knode adds the photos,
  recordings and videos in it and in its folders, down to three levels, as `knode import` does), or
- choose **File > Add Photos…** or **File > Add Recording or Video…**.

If a project is selected, what you add belongs to it. Knode takes HEIC, JPEG and PNG photos, audio
files (M4A, MP3, WAV, AAC, AIFF, CAF, FLAC, Voice Memos) and videos (MOV, MP4, M4V).

From the terminal:

```sh
knode import ~/Desktop/whiteboard.heic
knode import ~/Movies/talk.mov
knode import --from media ~/Pictures/Trip    # every photo, recording and video in the folder
```

The app reads and transcribes what you import this way the next time it opens the space.

Each file becomes one note, with the original kept unchanged as its attachment. Adding the same
file again finds the note it already has instead of making a second one.

### Photos

Knode reads each photo on your Mac with Apple's Vision:

- the **text in the photo** (a whiteboard, a slide, a receipt), in any language Vision reads,
  Spanish included;
- **what it shows**, as a few labels such as "whiteboard" or "document".

Both go into the note under *Text in the photo* and *What it shows*, so a search for "the
whiteboard photo" or for a word written on it finds the photo. The note's first line says when
and where the photo was taken and with which camera, from the photo's own metadata, and the same
details are kept as properties of the note (`photo.taken_at`, `photo.latitude`,
`photo.longitude`, `photo.camera`, `photo.width`, `photo.height`).

Knode keeps the coordinates, not the name of the place: naming a place from coordinates needs the
internet, and photos are read only on your Mac. A location a camera got wrong (coordinates that
cannot exist) is left out. To keep no location at all for a space:

```sh
knode spaces photo-location Work off
```

Turning it off also removes the location Knode already wrote for the photos of that space: the
coordinates in the note's properties and the place in its first line.

**The attached original keeps its own EXIF data, location included.** Knode never changes the
file you add, so a photo's GPS coordinates stay inside the attached file, and anyone or any agent
that can open the attachment can read them. If you do not want a photo's location in the space at
all, remove it from the file before you add it (for example, by leaving out the location when
you export the photo from Photos).

If a photo cannot be read, the Knode menu bar menu shows it with **Retry**.

### Recordings and videos

Audio files and videos are transcribed on your Mac the same way as meetings: Apple's on-device
speech recognition writes a transcript note, with times, linked to the recording, and, where Apple
Intelligence is available and the space has minutes on, Apple's on-device model writes the summary,
decisions and action items into the recording's note. For a video, Knode transcribes its sound.

A space keeps files of up to 100 MB. A recording or a video larger than that is not refused: Knode
takes its sound on your Mac as a small audio file (about 30 MB an hour), keeps that in the space
and transcribes it, and says in the note that the original stays where it was. The original file
is never changed. A photo larger than 100 MB, or a recording so long that even its sound is larger
than 100 MB, is not added, and Knode says why.

Some places require everyone recorded to agree to it. Recordings of people that you add are your
responsibility, as with meetings.

### Privacy

Photos, recordings and videos stay on your Mac and go only into the space you pick. Reading,
transcribing and summarising all run on the device; no image, audio, video or text is sent to any
server.

# Photos library and storage (user docs draft, for knodemd/docs)

## Photos library (v0.4)

Bring the photos and videos of the albums you choose in Photos into a space.

1. Open **Settings > Sources** and choose **Connect** on **Photos library**.
2. **Access**: Knode asks for Photos access now, not before. If you said no earlier, choose
   **Check Again** and allow Knode in **System Settings > Privacy & Security > Photos**.
3. **History**: **From today** brings in only what you take from now on; **Everything** brings in
   what is already in the albums too.
4. **Albums**: switch on the albums Knode reads. Nothing is read until you pick one.

What happens:

- Each photo becomes a photo note with its original file (HEIC or JPEG, unchanged), the date,
  place and camera, and the text and things your Mac finds in it.
- Each video becomes a recording with the video attached and a transcript made on your Mac. A
  video larger than 100 MB keeps only its audio; the video stays in Photos.
- New photos and videos in the picked albums come in by themselves. Nothing is added twice, even
  if your library is rebuilt or the same photo is in the library of another Mac.
- Albums are picked on each Mac: every Mac has its own Photos library. If a space reads albums
  picked on another Mac, this one says **No albums picked on this Mac** until you pick some here.
- A photo you added to the space yourself stays yours: the Photos library adds its own note for
  it, and the file is kept once.
- A photo you delete in Photos, take out of an album, or whose album you switch off stays in the
  space, marked as removed from the Photos library. It comes back to normal if it returns. The
  mark is kept apart from the note's text, so it stays when the text is read again.
- A photo or video you delete from the space is not brought back.
- Videos that can never be added (even their audio is larger than 100 MB) are skipped from then
  on; the source's card says how many.
- Photos kept only in iCloud wait until your Mac has the original. To download them, turn on
  **Download from iCloud** in the source's card.
- Everything happens on your Mac. Knode never changes your Photos library and sends nothing
  anywhere.

## Storage (v0.4)

**Settings > Spaces**, then choose a space, shows **Storage**:

- The total size of the space, and how much is notes, photos, videos, audio and other files.
  "Deleted" is files of deleted notes, freed the next time the space is purged.
- Who added what: a source such as the Photos library, an agent, or you.
- The largest files. **Show** opens the note; **Remove** takes the file out of the note (after
  asking) and keeps its text, such as what was read or transcribed. You can undo it from the
  history.
- **Warn past**: once a space is larger than this (10 GB unless you change it), adding files asks
  first and the Photos library stops adding new items until there is room. **Never** turns it off.
- One file is at most 100 MB: a video larger than that keeps only its audio for the transcript,
  and the original stays where it was.

Sizes are computed on your Mac.

## Knode Service and the menu bar

When Knode Service is running, the app opens your spaces through it, like your agents do, so a note
an agent writes appears in the list as it is written, and the note you are reading updates when an
agent changes it, even while you edit it (from v0.3). When the service is not running, the app opens the
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
- search by meaning: the model, how far each open space is indexed, a switch to turn it on or off
  and **Index Now** (from v0.3);
- **Agents and Access…**, to open the permissions screen;
- **Quit Knode**.

Closing the window keeps Knode in the menu bar; quit it from the menu.

## The knode command

The app carries the `knode` command inside. The first time you open it, it offers to install the
command so your terminal and your agents can use it; you can also install or uninstall it from the
Knode menu. If `knode` is already installed with Homebrew, the app uses that one. To connect your
agents, see [Connect your agents](#connect-your-agents) and [Install](01-install.md#the-mac-app).

## Next

See the [roadmap](https://knode.md/plan) for the rest.

## Guided project and vault import

Use **Get started** or **Import projects** to analyze an existing vault/project or start empty,
review and edit groupings, and create a separate Knode copy. The source is not moved or replaced.
The completion screen offers explicit Codex setup and optional instruction-file installation.
Use **Review import / archive** in the imported space's sidebar menu to recoverably archive a
trial and start again. See [the guided import tutorial](06-project-workflow.md#guided-import-in-the-mac-app)
for supported content, recovery behavior and the limits of folder-based organization.
