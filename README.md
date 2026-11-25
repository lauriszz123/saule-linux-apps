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

`lib/fileskit/` is not an app but a package the file browser and the editor
share: reading a folder, what a file is and how its size and date are said,
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
