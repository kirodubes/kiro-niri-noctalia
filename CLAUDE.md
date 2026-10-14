# kiro-niri — Claude project instructions

## Overview
Config package for the **Kiro niri edition** — the scrollable-tiling member of the KIROTUX
Wayland line (sibling to [kiro-hyprland](../kiro-hyprland/CLAUDE.md)). Public, open-core, shipped
via `nemesis_repo`. Full research + decisions live in the internal `Kiro-HQ/Kirotux/study-of-niri.md`.

## Edition spec (the WM-variable matrix)
- **Compositor:** niri (scrollable-tiling, Smithay-based — *not* wlroots).
- **Config language:** KDL. `etc/skel/.config/kiro-niri-noctalia/config.kdl` `include`s `cfg/*.kdl`
  (`animation, autostart, keybinds, input, display, layout, rules, misc`). Edit the `cfg/` file,
  not a monolith.
- **Desktop shell:** **noctalia v5** (native, `[extra]/noctalia`) — bar, launcher, lock, notifications,
  wallpaper, control center, session menu, built-in polkit agent. Driven over
  `noctalia msg <command>`. (Moved off v4/Quickshell 2026-10-14: it broke on Qt 6.12.) niri is only the compositor.
- **Autostart:** `spawn-sh-at-startup "noctalia"` (+ explicit `xdg-user-dirs-update`,
  + the archiso-gated Calamares line). niri does **not** process `/etc/xdg/autostart`.
- **Theming:** noctalia owns runtime accent colours (matugen internally). Base GTK look + cursor
  (dark adw-gtk3, Bibata-Modern-Ice) shipped via `/etc/dconf/`, owned by `kiro-wayland-dotfiles`
  (this edition is a partial consumer — dconf only, no mako/hypr/waybar).
- **Dependency note:** `noctalia` (v5) comes from Arch **`[extra]`** — **do not** repackage it into
  `nemesis_repo`. Never depend on `noctalia-shell`: [cachyos] ships v5 under that name without `shell.qml`.

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
  instead of noctalia. **Co-installable** — each edition ships its own namespaced config
  folder (`~/.config/kiro-niri-noctalia/` vs `~/.config/kiro-ohmyniri/`) and its own session that
  points niri there via `NIRI_CONFIG`, so there are no shared paths. The old reciprocal
  `conflicts=('kiro-ohmyniri')` was **dropped** (2026.07.03); both can be installed and picked
  per-login.

## Build / delivery
- Source-of-truth for the config; delivered as the `kiro-niri-noctalia` package via
  `~/KIRO-PKG-BUILD-APPS/kiro-niri-noctalia/build.sh` (public recipe → `~/EDU/nemesis_repo/`). After
  editing here: rebuild the package (recipe `build.sh`), then the ISO to test a fresh install.
- See [KIROTUX/CLAUDE.md](../../KIROTUX/CLAUDE.md) for the full KIROTUX delivery architecture.
