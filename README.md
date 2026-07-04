# kiro-niri

The **niri desktop edition** of Kiro — the scrollable-tiling member of the Kiro Wayland line (sibling to [kiro-hyprland](https://github.com/kirodubes/kiro-hyprland)).

## What it is

A configuration package: the source-of-truth config tree for Kiro's niri edition. niri is a
scrollable-tiling Wayland compositor; the desktop shell (bar, launcher, lock screen,
notifications, wallpaper, control center) is provided by **noctalia-shell** (Quickshell), driven
over `qs -c noctalia-shell ipc call …`. niri is the compositor; noctalia is everything else.

## What it ships

- `etc/skel/.config/kiro-niri-noctalia/` — the niri config, modular: `config.kdl` `include`s `cfg/*.kdl`
  (`keybinds`, `input`, `layout`, `rules`, `misc`, `animation`, `autostart`, `display`), plus a
  `keybindings.txt` cheat sheet and the Kiro wallpaper.
- `etc/skel/.config/noctalia/plugins.json` — enables the noctalia polkit-agent plugin.
- `etc/dconf/` — system-wide dark GTK + Kiro cursor defaults.

## How to install

```sh
sudo pacman -S kiro-niri-noctalia
```

`kiro-niri-noctalia` depends on `niri` + `noctalia-shell` (both resolved from chaotic-aur / cachyos) plus
the usual Wayland helpers. On a fresh login niri starts noctalia-shell, which paints the bar and
wallpaper. Press **Super + Ctrl + S** for the searchable keybindings cheat sheet, or
**Super + Shift + /** for niri's built-in hotkey overlay.

A pristine copy of the config is kept at `/usr/share/kiro/kiro-niri-noctalia/` so it can be restored.
