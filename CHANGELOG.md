# Changelog

## 2026.06.30

### What Changed
- **Initial config package** for the Kiro niri edition — the scrollable-tiling member of the
  KIROTUX Wayland line. niri compositor + noctalia-shell (Quickshell v4) as the desktop shell.

### Technical Details
- niri config is modular: `etc/skel/.config/niri/config.kdl` `include`s `cfg/*.kdl`
  (`animation`, `autostart`, `keybinds`, `input`, `display`, `layout`, `rules`, `misc`),
  modelled on CachyOS's niri+noctalia settings with Kiro's SUPER-based keybind scheme and app
  defaults layered on (CTRL+ALT launchers, SUPER+F1..F12 app keys, `kiro-keybindings` on
  SUPER+CTRL+S). Keyboard layout `be,us`, Kiro-blue focus ring, Bibata-Modern-Ice cursor.
- Desktop shell is **noctalia-shell**, started by the single autostart line
  `spawn-sh-at-startup "qs -c noctalia-shell"`; launcher / lock / control center / settings /
  wallpaper / volume / brightness / media all routed through `qs -c noctalia-shell ipc call …`.
  niri's media/brightness keys carry `allow-when-locked=true`.
- `noctalia-shell` (+ `noctalia-qs`) are pulled from chaotic-aur / cachyos — no in-house
  packaging needed. GTK base look + cursor delivered via `etc/dconf/`.
- Research and decisions recorded in `Kiro-HQ/Kirotux/study-of-niri.md`.

### Files Modified
- `etc/skel/.config/niri/config.kdl` + `cfg/*.kdl` + `keybindings.txt` + `bg/kiro.jpg`
- `etc/skel/.config/noctalia/plugins.json`
- `etc/dconf/db/local.d/00-kiro.conf`, `etc/dconf/profile/user`
- `README.md`, `CLAUDE.md`, `up.sh`, `setup.sh`, `.gitignore`
