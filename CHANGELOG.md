# Changelog

## 2026.10.14

### What Changed
- Moved the edition to noctalia v5. noctalia-shell v4 (Quickshell) broke on Qt 6.12: noctalia-qs was built against Qt 6.11, so every themed widget got an undefined colour and the bar rendered as empty pills. v4 is unmaintained upstream and [cachyos] now ships v5 under the noctalia-shell name without shell.qml. Native noctalia v5.2.1 from Arch [extra] was live-tested on a real-metal test box and works.
- `Ctrl+Alt+H` opens Kirotux Niri Premium, as on kiro-niri-dms and kiro-hyprland-noctalia. Without the app it shows a notification with a link to get it.
- `config.kdl` loads Kirotux Niri Premium's `kirotux-appearance.kdl` as its last line, as on kiro-niri-dms. The app's look changes now apply straight away; before, it first asked to add the line to your config.
- **The installer tiles again on real hardware.** Calamares now floats (1024x900) only when the live ISO runs in a virtual machine, where the floating window is still needed to keep the Next button clickable. On real metal it opens as a normal tiled window.

### Technical Details
- All 20 noctalia key bindings in `cfg/keybinds.kdl` now call `noctalia msg` (panel-toggle launcher/control-center/wallpaper, settings-toggle, session lock, volume-up/-down/-mute, mic-mute, media next/previous/toggle, brightness-up/-down).
- `cfg/autostart.kdl` starts `noctalia`. The existing `debug { honor-xdg-activation-with-invalid-serial }` and the `^noctalia-wallpaper` backdrop layer-rule already match v5. `niri validate` passes. Recipe depends on `noctalia`.
- Premium bind: same `spawn-sh` line as kiro-niri-dms (`command -v kirotux-niri-premium` → run it, else `notify-send` with a Get KiroTux action). Added to `keybindings.txt` too. `niri validate` passes.
- Premium include: `include optional=true "kirotux-appearance.kdl"` with niri-dms's comment block, kept LAST so the app's values override the edition's. `optional=true` (niri 26.04+) makes a missing file fine. Matches `knp_niri.INCLUDE_LINE` exactly, so `include_present()` sees it.
- The Calamares `window-rule` moved from `cfg/rules.kdl` to the new `cfg/calamares-vm.rule` (no `.kdl`, so niri never reads it on its own). `rules.kdl` now has `include optional=true "calamares-vm.kdl"`. The live-ISO autostart line runs `systemd-detect-virt -q` and, in a VM, copies the rule to `cfg/calamares-vm.kdl`, then waits `sleep 1` while niri hot-reloads the new include before it starts `calamares_polkit`. Tested live on a real-metal box: niri accepts the include while the file is missing, picks the file up when it appears at runtime (next Calamares opens floating 1024x900), and with no VM the installer tiles. The line stays a single line inside the "Live ISO only" comment block, so kiro_final's `sed '/Live ISO only/,/calamares_polkit/d'` still strips it on install. `niri validate` passes with and without the file.

### Files Modified
- etc/skel/.config/kiro-niri-noctalia/cfg/keybinds.kdl, cfg/autostart.kdl, cfg/layout.kdl, config.kdl, keybindings.txt
- usr/share/wayland-sessions/kiro-niri-noctalia.desktop
- README.md, CLAUDE.md
- etc/skel/.config/kiro-niri-noctalia/cfg/rules.kdl, cfg/autostart.kdl, cfg/calamares-vm.rule (new)

## 2026.10.10

### What Changed
- Source repo moved from `~/KIROTUX/kiro-niri-noctalia` to `~/KIRO/kiro-niri-noctalia` and its recipe to `~/KIRO-PKG-BUILD-APPS/kiro-niri-noctalia`: ATT installs it from nemesis_repo, so it is a Kiro package, not KiroTux-only. Paths and links in the docs follow. The package itself is unchanged.
- **The installer's Next button can be clicked again:** Calamares now opens as a floating 1024x900 window. In a QEMU VM the Next button ignored mouse clicks; floating the window fixed it.

### Technical Details
- Calamares runs on XWayland (`QT_QPA_PLATFORM=xcb` from `calamares_polkit`). xwayland-satellite kept the X
  screen at 1698x952 after the VM display grew to 1920x1080, so the bottom of the 1020 px tall tiled window,
  where the buttons are, sat outside the X screen and received no clicks. New `window-rule` on
  `app-id="^calamares$"`: `open-floating true` + fixed `default-column-width`/`default-window-height`.
  Checked with `niri validate` on niri 26.04.

### Files Modified
- `CLAUDE.md`, `CHANGELOG.md`
- `etc/skel/.config/kiro-niri-noctalia/cfg/rules.kdl`

## 2026.10.05

### What Changed
- **`QT_STYLE_OVERRIDE "kvantum"` moved into the `environment { }` block** of `cfg/misc.kdl`. The Wayland ISOs keep only
  `EDITOR` in `/etc/environment` from now on (`GTK_THEME` and `BROWSER` there caused transparent GTK 4 windows and a
  browser default that couldn't be changed); the Qt variables belong to the session.

### Technical Details
- Same `KEY "value"` form as the existing lines; not validated with `niri validate` (no niri install at hand).

### Files Modified
- `etc/skel/.config/kiro-niri-noctalia/cfg/misc.kdl`

## 2026.09.27

### What Changed
- Reworded the `keybindings.txt` header: dropped "DO NOT EDIT BY HAND" and the generator name, added a line telling users it lists the default bindings and they can edit it to match their own. A user changed a binding, expected the file to update itself, and went looking for a generator that isn't part of Kiro.

### Technical Details
- `Generated:` now carries only the date, so the kiro-keybindings HTML/PDF footer (which prints everything after `Generated:`) shows a clean date. The new line is a `#` comment, which both kiro-keybindings parsers skip. Bindings unchanged.

### Files Modified
- `etc/skel/.config/kiro-niri-noctalia/keybindings.txt`

## 2026.07.09

### Docs: fix stale config path, ship-list, and dropped-conflict claim

**What Changed**
- **CLAUDE.md config path** updated `etc/skel/.config/niri/` → `kiro-niri-noctalia/` (the folder
  was namespaced on 2026.07.04).
- **CLAUDE.md "Sibling edition"** rewritten: it claimed a reciprocal
  `conflicts=('kiro-ohmyniri')` and that the two editions "can never coexist" — but the conflict
  was **dropped** (2026.07.03) and both are co-installable via namespaced folders + `NIRI_CONFIG`.
- **README "What it ships"** dropped `etc/skel/.config/noctalia/plugins.json` (moved to
  `kiro-noctalia`) and `etc/dconf/` (moved to `kiro-wayland-dotfiles`) — neither is in this
  package; noted they come from those dependencies.

**Files Modified**
- `README.md`, `CLAUDE.md`

## 2026.07.07

### Keyboard: US default + normalized Alt+Shift toggle

**What Changed**
- Flipped the layout order `be,us` → **`us,be`**: US QWERTY is now the default at login, Belgian AZERTY the secondary layout.
- Normalized the layout-switch option from `grp:alts_toggle` (press both Alts) to **`grp:alt_shift_toggle`** (Alt+Shift), matching the rest of the KIROTUX line. Options now `grp:alt_shift_toggle,compose:caps`.

**Technical Details**
- `grp:alt_shift_toggle` matches the CachyOS Calamares reference (`keyboard/Config.cpp` defaults the group switcher to it when a second layout exists). `compose:caps` (Caps = Compose) unchanged. `layout` / `options` in `cfg/input.kdl`.

**Files Modified**
- `etc/skel/.config/kiro-niri-noctalia/cfg/input.kdl`

## 2026.07.04

### Shared noctalia config extracted to `kiro-noctalia`
- Removed the `etc/skel/.config/noctalia/` folder from this package and added
  `kiro-noctalia` to `depends=()`. noctalia reads the fixed path
  `~/.config/noctalia/`, so the identical config shipped here and by
  `kiro-hyprland-noctalia` caused a pacman file conflict on
  `/etc/skel/.config/noctalia/settings.json` — the editions could not
  co-install. The shared config now has a single owner, `kiro-noctalia`.
- The niri compositor config (`~/.config/kiro-niri-noctalia/`), session, wrapper,
  hide script/hook are unchanged and still shipped here; only the shared
  `noctalia/` skel folder moved out. The golden copy at
  `/usr/share/kiro/kiro-niri-noctalia/` now contains only this edition's config.
- Files: removed `etc/skel/.config/noctalia/*`;
  `../KIROTUX-PKG-BUILD/kiro-niri-noctalia/PKGBUILD` (depends + comment).

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
