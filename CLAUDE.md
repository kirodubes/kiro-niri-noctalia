# kiro-niri — Claude project instructions

## Overview
Config package for the **Kiro niri edition** — the scrollable-tiling member of the KIROTUX
Wayland line (sibling to [kiro-hyprland](../kiro-hyprland/CLAUDE.md)). Public, open-core, shipped
via `nemesis_repo`. Full research + decisions live in the internal `Kiro-HQ/Kirotux/study-of-niri.md`.

## Edition spec (the WM-variable matrix)
- **Compositor:** niri (scrollable-tiling, Smithay-based — *not* wlroots).
- **Config language:** KDL. `etc/skel/.config/niri/config.kdl` `include`s `cfg/*.kdl`
  (`animation, autostart, keybinds, input, display, layout, rules, misc`). Edit the `cfg/` file,
  not a monolith.
- **Desktop shell:** **noctalia-shell** (Quickshell v4) — bar, launcher, lock, notifications,
  wallpaper, control center, session menu, polkit agent (via its plugin). Driven over
  `qs -c noctalia-shell ipc call <module> <action>`. niri is only the compositor.
- **Autostart:** `spawn-sh-at-startup "qs -c noctalia-shell"` (+ explicit `xdg-user-dirs-update`,
  + the archiso-gated Calamares line). niri does **not** process `/etc/xdg/autostart`.
- **Theming:** noctalia owns runtime accent colours (matugen internally). Base GTK look + cursor
  (dark adw-gtk3, Bibata-Modern-Ice) shipped via `/etc/dconf/`, owned by `kiro-wayland-dotfiles`
  (this edition is a partial consumer — dconf only, no mako/hypr/waybar).
- **Dependency note:** `noctalia-shell` (+ `noctalia-qs`) come from **chaotic-aur / cachyos**
  (both in Kiro's `pacman.conf`) — **do not** repackage them into `nemesis_repo`.

## Keybindings
- niri-native window/column/workspace management (column model: `focus-column-*`,
  `move-column-*`, consume/expel) **kept verbatim**; Kiro's app scheme layered on a
  collision-free space: CTRL+ALT launchers + SUPER+F1..F12 + `kiro-keybindings` on SUPER+CTRL+S.
- `keybindings.txt` mirrors `cfg/keybinds.kdl` — keep them in lockstep; a duplicate-chord scan
  must pass. (`kiro-keybindings` and `/kiro-create-keybindings` still need **niri** added to
  their WM-detection table — known gap, same one Hyprland hit.)
- Mod = Super. Belgian `be,us` layout (matches the rest of the Kiro line).

## Patterns / gotchas
- niri `spawn` is **argv, no shell**; use `spawn-sh "…"` for anything with pipes/`&&`/globs
  (e.g. the noctalia IPC calls and the archiso-gated installer line).
- niri has **no native lock/idle** — noctalia provides both; do not add swaylock/swayidle.
- `debug { honor-xdg-activation-with-invalid-serial }` is required for noctalia notification
  actions / window activation — keep it.
- Wallpaper is drawn by noctalia into niri's backdrop (`layer-rule … place-within-backdrop true`
  + `layout { background-color "transparent" }`) — no separate wallpaper daemon.

## Sibling edition
- **`kiro-ohmyniri`** — same compositor, kiro-hyprland's shell (waybar/mako/swaybg/rofi/gtklock)
  instead of noctalia-shell. `conflicts=('kiro-ohmyniri')` here (reciprocal) — both ship the same
  `/etc/skel/.config/niri/**` paths, and niri always reads `~/.config/niri/config.kdl`, so the
  two editions can never usefully coexist. Pick one.

## Build / delivery
- Source-of-truth for the config; delivered as the `kiro-niri` package via
  `../KIROTUX-PKG-BUILD/kiro-niri/build.sh` (public recipe → `~/EDU/nemesis_repo/`). After
  editing here: rebuild the package (recipe `build.sh`), then the ISO to test a fresh install.
- See [../CLAUDE.md](../CLAUDE.md) for the full KIROTUX delivery architecture.
