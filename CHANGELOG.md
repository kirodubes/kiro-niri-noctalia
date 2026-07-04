# Changelog

## 2026.07.04

### What Changed
- **Full namespace rename `kiro-niri` → `kiro-niri-noctalia`.** Establishes the `kiro-niri-<shell>`
  family convention (sibling `kiro-niri-mds`, niri + DankMaterialShell). The config folder, session,
  wrapper, hide script and hook all move to the `kiro-niri-noctalia` namespace so this edition and
  its niri siblings share no file paths and are co-installable. The SDDM session display name also
  becomes **"Kiro Niri Noctalia"** (was "Kiro Niri"), matching the siblings "Kiro OhMyNiri" / "Kiro
  Niri MDS".
- The package is now `kiro-niri-noctalia` (`replaces=('kiro-niri')` migrates old installs). This
  **GitHub repo keeps its `kiro-niri` name** — only the package + shipped paths were renamed.

### Technical Details
- `etc/skel/.config/kiro-niri/` → `etc/skel/.config/kiro-niri-noctalia/` (relative `./cfg/*.kdl`
  includes come along untouched).
- `usr/bin/kiro-niri-session` → `kiro-niri-noctalia-session` (`NIRI_CONFIG` →
  `~/.config/kiro-niri-noctalia/config.kdl`).
- `usr/bin/kiro-niri-hide-upstream-session` → `kiro-niri-noctalia-hide-upstream-session`;
  hook renamed to match, its `Exec` updated.
- `usr/share/wayland-sessions/kiro-niri.desktop` → `kiro-niri-noctalia.desktop` (`Exec` updated;
  `Name=` changed "Kiro Niri" → "Kiro Niri Noctalia").

### Files Modified
- `etc/skel/.config/kiro-niri/` → `etc/skel/.config/kiro-niri-noctalia/` (renamed)
- `usr/bin/kiro-niri-noctalia-session`, `usr/bin/kiro-niri-noctalia-hide-upstream-session` (renamed)
- `usr/share/wayland-sessions/kiro-niri-noctalia.desktop` (renamed)
- `usr/share/libalpm/hooks/kiro-niri-noctalia-hide-upstream-session.hook` (renamed)
- `README.md`, `CLAUDE.md`

## 2026.07.03

### What Changed
- **Own session entry + own config folder (`~/.config/kiro-niri/`).** kiro-niri and kiro-ohmyniri
  both boot the upstream `niri.desktop` and shared `~/.config/niri/`, so switching editions
  inherited the other's stale config — found live on picard, where a "Kiro Niri" login was running
  ohmyniri's swaybg/variety stack instead of noctalia. Now this edition ships **`kiro-niri.desktop`**
  ("Kiro Niri") → a **`kiro-niri-session`** wrapper that points niri at `~/.config/kiro-niri/config.kdl`
  via **`NIRI_CONFIG`**, and the whole niri config tree moved from `etc/skel/.config/niri/` to
  `etc/skel/.config/kiro-niri/` (relative `./cfg/*.kdl` includes come along untouched). The upstream
  plain "Niri" greeter entry is hidden with a `NoDisplay=true` pacman hook (same pattern as kiro-mango)
  so it can't be picked by mistake (it would read the now-unused `~/.config/niri/`).
- **Dropped `conflicts=('kiro-ohmyniri')` — the two editions can now be co-installed.** The conflict
  only existed because both shipped `etc/skel/.config/niri/`; with per-edition folders/entries/wrappers
  they share no files. Both "Kiro Niri" and "Kiro OhMyNiri" can now sit in the greeter, switchable
  per-login. The remove-hook un-hide of upstream "Niri" is guarded to only fire when the sibling
  edition is gone (else removing one would un-hide it while the other still needs it hidden).

### Technical Details
- `kiro-niri-session`: sets `NIRI_CONFIG` (both `export` and `systemctl --user set-environment`), then
  `exec niri-session`. niri-session's own `systemctl --user import-environment` propagates it into
  `niri.service`.
- Hide hook + helper (`usr/bin/kiro-niri-hide-upstream-session`, `usr/share/libalpm/hooks/…`): `.install`
  applies on first install (hook doesn't fire when niri is already present), hook re-applies after niri
  upgrades, `post_remove` strips it back out.
- Pairs with `archlinux-logout-gtk4` 26.07-07: `DESKTOP_SESSION=kiro-niri` now distinguishes the edition
  directly, so its logout uses `niri msg action quit -s` (clean systemd session exit) with no runtime probe.

### Files Modified
- `etc/skel/.config/niri/` → `etc/skel/.config/kiro-niri/` (moved)
- [etc/skel/.config/kiro-niri/config.kdl](etc/skel/.config/kiro-niri/config.kdl) (header path)
- [usr/bin/kiro-niri-session](usr/bin/kiro-niri-session) (new)
- [usr/share/wayland-sessions/kiro-niri.desktop](usr/share/wayland-sessions/kiro-niri.desktop) (new)
- [usr/bin/kiro-niri-hide-upstream-session](usr/bin/kiro-niri-hide-upstream-session) (new)
- [usr/share/libalpm/hooks/kiro-niri-hide-upstream-session.hook](usr/share/libalpm/hooks/kiro-niri-hide-upstream-session.hook) (new)
- [../KIROTUX-PKG-BUILD/kiro-niri/kiro-niri.install](../KIROTUX-PKG-BUILD/kiro-niri/kiro-niri.install)
- [../KIROTUX-PKG-BUILD/kiro-niri/PKGBUILD](../KIROTUX-PKG-BUILD/kiro-niri/PKGBUILD) (pkgrel 07 → 09)

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
