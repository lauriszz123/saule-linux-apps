# Saule Linux apps

The apps that ship with [Saule Linux](https://github.com/lauriszz123/saule-linux),
one Saule project per folder. Each is built into the image's `/bin/apps`
and opened from the launcher (Super+Space); none starts on its own.

| Folder | App | |
|---|---|---|
| `settings/` | Settings | the computer's and the window manager's settings, macOS style |
| `about/` | About This System | what the computer is, like a Mac's About This Mac; opened from the menu behind the sun |
| `files/` | Files | the computer's folders and what is in them, like a Mac's Finder |
| `editor/` | Editor | plain text in a window, like a Mac's TextEdit in its plain-text mode |
| `terminal/` | Terminal | a shell in a window, like a Mac's Terminal |
| `task-manager/` | Task Manager | what is running and how hard the computer is working, like a Mac's Activity Monitor |
| `preview/` | Preview | pictures, PDF documents and Markdown files, shown and not changed, like a Mac's Preview |
| `clock/` | Clock | the time and the date, a stopwatch and a timer |
| `calculator/` | Calculator | the four operations, percent and sign, like a Mac's Calculator in its basic form |
| `calendar/` | Calendar | the month at a glance and the events of each day |

`lib/fileskit/` is not an app but a package the file browser, the editor
and Preview share: reading a folder, what a file is and how its size and date are said,
the Trash, the pictures of a folder and a document, and the panel an app
asks "which file?" with. It is a folder deeper than the apps so that
building every app (below) leaves it alone.

They talk to the window manager, [saule-wm](https://github.com/lauriszz123/saule-wm),
through its `saule.wm` service, whose interface is the `wmapi` package in
`saule-wm/api`. Each app depends on it by path, so this repository sits
beside `saule-wm`:

```
saule-wm/
saule-linux/
saule-linux-apps/
```

## Settings' panes

Listed in groups, as a Mac's are, and only where the computer has what a
pane is about. The window manager says what it has (`wmapi`'s
`MachineInfo`), and is asked again every second, so a mouse plugged in
brings its pane and one taken out takes it away.

| Group | Pane | Shown |
|---|---|---|
| Connections | Wi-Fi | with a radio |
| | Bluetooth | with a Bluetooth radio |
| | Network | with a place for a cable, or a radio |
| Sound | Sound | with a sound card |
| The computer | General (its name; and About, Storage, Date & Time, which it opens) | always |
| | Displays (and brightness, with a backlight) | always |
| | Appearance, Desktop & Windows, Wallpaper | always |
| Who may use it | Login Password | on Saule Linux with a disk |
| Input | Keyboard (layouts, shortcuts) | unless the computer is known to have none |
| | Mouse | while there is a mouse |
| | Trackpad | with a trackpad |

Nested in another desktop the window manager has no list of devices and
nothing answers for the computer: the panes about the window manager
itself are shown, with Keyboard and Mouse as they always were.

## Files

A sidebar of places (the home and its Documents, Downloads and Desktop; the
computer, the data disk, the Trash), a bar (back, forward, list or icons, a
new folder, a search of the names shown), the folder itself, and the way to
it along the foot with how much room its disk has left. A double click
opens: a folder is gone into, a text file opens in the Editor, which the
window manager is asked to start (`WmSettings.launch`). A right click
brings a menu: open, open in Terminal, rename, duplicate, copy, cut, move
to Trash. The Trash is `~/.Trash`; only emptying it removes anything.

The keys are a Mac's with Ctrl where it has Command: Ctrl+O opens, Ctrl+Up
goes to the folder above, Ctrl+[ and Ctrl+] back and forward, Enter
renames, Ctrl+Shift+N makes a folder, Ctrl+C/X/V/D copy, cut, paste and
duplicate, Ctrl+Backspace throws away, Ctrl+Shift+. shows hidden files,
Ctrl+1 and Ctrl+2 choose icons or the list; a letter typed chooses by
name.

Drives that are plugged in are listed under Locations, each with a mark
that ejects it (the system mounts them: saule-linux's `disks.rs`).

Saule can list a folder's names and no more, so sizes and dates are asked
of the system's `stat`, and whole folders are copied and removed by its
`cp` and `rm` (BusyBox's, in the image). One thing is chosen at a time, and
a large copy holds the window until it is done.

## Editor

One document a window: a toolbar (New, Open…, Save; the text smaller or
larger; lines wrapped or not), the page, and where the document is kept
along the foot. Open and Save As use fileskit's panel, as a sheet over the
window. Ctrl+N, Ctrl+O, Ctrl+S, Ctrl+Shift+S; Ctrl+= and Ctrl+-.

New, Open and closing the window ask first when there are changes not
saved (`App.onCloseRequest`). Its settings (Ctrl+comma, or "Settings…" in
the menu under the app's name): the system's typeface or a fixed-width
one, the text's size, and whether long lines wrap; kept in
`~/.config/saule/editor.conf`. Text only, up to 600 kB.

## Terminal

A shell (`$SHELL`, else `sh`) behind a pseudo-terminal, in a grid of a
fixed-width typeface. It understands xterm's escape sequences as far as a
shell's line editing, `ls --color`, a pager, `vi` and `top` use them: the
cursor's movements, erasing, inserting and deleting, scrolling regions, the
alternate screen, sixteen, 256 and full colours, bold, underline and
inverse, the window's title. 3000 lines of history, read back with the
wheel or Shift+Page Up. Dragging marks text; Ctrl+Shift+C copies it and
Ctrl+Shift+V pastes. Started with a folder, the shell starts there: Files'
"Open in Terminal". The window closes when the shell ends.

Its settings (Ctrl+comma) are the text's size, also changed with
Ctrl+Shift+= and Ctrl+Shift+-.

Not there: more than one tab or window per app, wide (CJK) characters in
two cells, the mouse reported to programs, and double-click word
selection.

## Task Manager

Four views, chosen in the bar:

- **Overview**: the processor, memory and the graphics chip, each how busy
  it is now and how it has been over the last two minutes. Few graphics
  chips say how busy they are (AMD's driver does, Intel's does not); the
  card says so where there is nothing to show.
- **Apps**: the apps that are open.
- **Processes**: every process, all of them, the person's own or the
  system's, searched by name or number and ordered by any column.
- **Startup**: what was started with the system and is running: its
  services and the window manager.

Quit (the bar's button, Delete, or Ctrl+Backspace) asks first, and offers to
force what does not answer. Only the person's own processes can be ended:
apps and what they started, not the system's services.

The figures come from `saule-tasks` (saule-linux's `system/src/tasks.rs`),
started once and read a sample from every other second. On another system,
where it is missing, the window says there is nothing to show;
`SAULE_TASKS=/path/to/saule-tasks` names one built elsewhere. A folder name
of several words is written with hyphens, which the window manager shows as
spaces: `task-manager` is "Task Manager".

## Preview

One app for three kinds of document, told apart by the file's name:

- **Pictures** (PNG and JPEG): shown as large as fits. The bar's − and +,
  Ctrl+- and Ctrl+=, or the wheel with Ctrl held make one smaller or larger;
  a larger one is moved by dragging or with the arrows. A double click goes
  between what fits and its real size.
- **PDF documents**: a page at a time, as wide as the window. The bar's ‹
  and ›, the arrows, Page Up and Page Down turn the pages, and so does the
  wheel at a page's top and foot. Each page is drawn by `saule-pdf`
  (saule-linux's `pdf/`) at the width it is shown at, into a picture kept
  under `~/.cache/saule/preview` for as long as the app runs; a page a
  window is being dragged across is drawn once the width has stood for a
  moment. `SAULE_PDF=/path/to/saule-pdf` names one built elsewhere.
- **Markdown** (`.md`, `.markdown`): headings, paragraphs with bold, italic,
  code and links, lists and task lists, quotes, code blocks, tables, rules
  and pictures from files beside it. Edit, or Ctrl+E, opens the file in the
  editor. Links are shown and not followed; a picture on the web is not
  fetched.

Files opens these three kinds in Preview; a Markdown file's menu has Open in
Editor as well. Ctrl+O opens another file in the same window.

Not there: more than one page of a PDF in view, searching and selecting
text, printing, GIF and SVG pictures, and HTML inside Markdown.

## Clock

Three views, chosen in the bar or with Ctrl+1 to Ctrl+3: the time on a face
and in figures with the date, a stopwatch with laps, and a timer set from a
list of lengths or a minute at a time. Space starts and stops the one shown,
L takes a lap, R resets. A timer that runs out says so with a notification
through the window manager, as long as Clock is still open.

Not there: alarms, and clocks of other time zones.

## Calculator

Digits, the four operations, percent, the sign, and the answer, worked in
the order they are pressed as a pocket calculator works them: `2 + 3 × 4 =`
is 20. `=` pressed again does the last operation again. The keyboard's
digits and signs press the keys, Enter is `=`, Backspace takes a digit back,
Escape clears. Nine digits fit; an answer too large or too small for them is
shown as a power of ten, and one that is not a number as "Error".

## Calendar

The month, Monday first, each day with as many of its events as there is
room for; beside it (under it in a narrow window) the events of the day
chosen. A double click on a day, the bar's +, Return or Ctrl+N writes a new
event: a title, a time in steps of five minutes or an hour, or all day. A
double click on an event changes or deletes it; Delete removes the one
chosen. The arrows move by a day and a week, Page Up and Page Down and the
wheel by a month, T goes back to today.

Events are kept in `~/.local/share/saule/calendar.tsv`, a line each: the
date, the time or `-`, and the title, with tabs between.

Not there: events that repeat or last more than a day, reminders, and
calendars shared with other computers.

## Running one

Inside the window manager, as a window of it:

```sh
cd saule-linux-apps/settings && saule build -o /tmp/settings
cd ../../saule-wm && saule run . /tmp/settings
```

Run on its own, an app says it can't reach the window manager.

## In the image

`make` in saule-linux builds every folder here into `/bin/apps/<name>`,
named after its `saule.config` `name:`.
