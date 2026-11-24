# Saule Linux apps

The apps that ship with [Saule Linux](https://github.com/lauriszz123/saule-linux),
one Saule project per folder. Each is built into the image's `/bin/apps`
and opened from the launcher (Super+Space); none starts on its own.

| Folder | App | |
|---|---|---|
| `settings/` | Settings | the window manager's settings, macOS style: windows, appearance, wallpaper, about |
| `about/` | About This System | what the computer is, like a Mac's About This Mac; opened from the menu behind the sun |

They talk to the window manager, [saule-wm](https://github.com/lauriszz123/saule-wm),
through its `saule.wm` service, whose interface is the `wmapi` package in
`saule-wm/api`. Each app depends on it by path, so this repository sits
beside `saule-wm`:

```
saule-wm/
saule-linux/
saule-linux-apps/
```

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
