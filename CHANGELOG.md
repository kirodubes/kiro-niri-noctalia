# Changelog

## 2026.07.02

### What Changed
- **Became a partial consumer of `kiro-wayland-dotfiles`, for dconf only.** Previously shipped its
  own `/etc/dconf/profile/user` + `/etc/dconf/db/local.d/00-kiro.conf`, byte-identical to the
  other 8 editions — a real pacman file-conflict when co-installed with any other Kiro Wayland
  edition. Added `kiro-wayland-dotfiles` to `depends=()`; noctalia still owns everything else
  (mako/hypr/waybar are not part of this edition).

### Files Modified
- `etc/dconf/` (removed, moved to `kiro-wayland-dotfiles`)
- `CLAUDE.md` (theming bullet)

## 2026.07.01

### What Changed
- New sibling edition **`kiro-ohmyniri`** shipped (same niri compositor, kiro-hyprland's shell).
  Declared reciprocal `conflicts=('kiro-ohmyniri')` in this package's PKGBUILD — both editions
  ship the same `/etc/skel/.config/niri/**` paths and niri always reads
  `~/.config/niri/config.kdl`, so they can never usefully coexist.

### Files Modified
- `CLAUDE.md` (sibling-edition note)
- `KIROTUX-PKG-BUILD/kiro-niri/PKGBUILD` (`conflicts`)

## 2026.06.30

### What Changed
- **Initial config package** for the Kiro niri edition — the scrollable-tiling member of the
  KIROTUX Wayland line. niri compositor + noctalia-shell (Quickshell v4) as the desktop shell.
- **Catppuccin Mocha is the default look.** noctalia ships with `predefinedScheme: "Catppuccin"`
  (`useWallpaperColors: false`) plus the generated `colors.json` palette, and niri's focus ring
  is Catppuccin mauve (`#cba6f7`). Captured live from a running niri session, sanitized of
  personal paths.

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
- `etc/skel/.config/noctalia/plugins.json`, `settings.json` (Catppuccin), `colors.json` (palette)
- `etc/dconf/db/local.d/00-kiro.conf`, `etc/dconf/profile/user`
- `README.md`, `CLAUDE.md`, `up.sh`, `setup.sh`, `.gitignore`
