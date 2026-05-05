# omarchy-workspace-profiles

Named workspace profiles for [Omarchy](https://omarchy.org) on Hyprland.

Group your Hyprland workspaces into named profiles (`work`, `personal`,
`client-foo`, …). Each profile owns its own Chromium data dir, mako rules,
waybar layout, and `SUPER+1..5` slots. Switching the active profile rebinds
your numeric workspaces, swaps the default browser shim's target, and updates
the bar without touching unrelated state.

## Install

### Arch / Omarchy (AUR)

```sh
yay -S omarchy-workspace-profiles
omarchy-setup-workspace-profiles
```

### From source

```sh
git clone https://github.com/alansikora/omarchy-workspace-profiles
cd omarchy-workspace-profiles
sudo install -Dm755 bin/* -t /usr/bin/
sudo install -Dm644 config/hypr/*       -t /usr/share/omarchy-workspace-profiles/config/hypr/
sudo install -Dm644 config/waybar/*     -t /usr/share/omarchy-workspace-profiles/config/waybar/
sudo install -Dm644 config/omarchy/extensions/*.sh -t /usr/share/omarchy-workspace-profiles/config/omarchy/extensions/
omarchy-setup-workspace-profiles
```

`omarchy-setup-workspace-profiles` is idempotent — re-running it refreshes
keybindings, the browser .desktop, mako includes, and the waybar sidecar. It
does not clobber your profile config (`~/.config/omarchy/workspace-profiles.conf`).

## What it installs

After running setup, you will have:

- **Keybindings** — `~/.config/hypr/workspace-profiles.conf` overrides
  `SUPER+1..5` (and SHIFT/ALT/CTRL combos) to route through the active
  profile's named slots, plus `SUPER CTRL 1..9` to switch profile.
- **Autostart** — `~/.config/hypr/workspace-profiles-autostart.conf` runs
  `omarchy-workspace-profile boot` and the `-sync` daemon at session start.
- **Default browser** — `workspace-profile-browser.desktop` registered as
  the system-wide handler for `http`/`https`/`html`. Every link click routes
  through the active profile's Chromium user-data-dir.
- **Mako include** — adds a managed include line to `~/.config/mako/config`
  so per-profile notification rules can be loaded.
- **Waybar widget** — `~/.config/waybar/workspace-profiles.{jsonc,css}`
  sidecars that the controller updates on profile switch.
- **Menu extension** — `~/.config/omarchy/extensions/workspace-profiles.sh`
  adds a "Workspace Profiles" submenu to `omarchy-menu`.

Add `@import "./workspace-profiles.css";` and the JSON include to your
existing `~/.config/waybar/style.css` and `~/.config/waybar/config.jsonc`
respectively to render the workspace-profile widget.

## Usage

```sh
omarchy-workspace-profile on               # enable the feature, seed default profile
omarchy-workspace-profile off              # disable (sidecars stay, actions no-op)
omarchy-workspace-profile cycle            # next profile
omarchy-workspace-profile set-index 2      # switch to 2nd profile
omarchy-workspace-profile goto 3           # go to slot 3 of active profile
omarchy-workspace-profile move 4           # move focused window to slot 4 (follow)
omarchy-workspace-profile send-to-index 1  # send focused window to profile 1
```

Edit `~/.config/omarchy/workspace-profiles.conf` to add profiles and slot
names. Run `omarchy-workspace-profile reload` after editing.

## Disable / uninstall

```sh
omarchy-workspace-profile off              # stop active behavior
sudo pacman -R omarchy-workspace-profiles  # remove package
```

Sidecar files in `~/.config/hypr/`, `~/.config/waybar/`, and the registered
default browser remain — clean up manually if no longer wanted:

```sh
xdg-mime default chromium.desktop x-scheme-handler/http x-scheme-handler/https
xdg-settings set default-web-browser chromium.desktop
rm -f ~/.local/share/applications/workspace-profile-browser.desktop
rm -f ~/.config/hypr/workspace-profiles.conf ~/.config/hypr/workspace-profiles-autostart.conf
rm -f ~/.config/waybar/workspace-profiles.{jsonc,css}
rm -f ~/.config/omarchy/extensions/workspace-profiles.sh
```

## Requirements

- Omarchy (Hyprland + waybar + walker + mako stack)
- `python` (for the sync daemon)
- `xdg-utils`, `libnotify`
- A Chromium-family browser for the per-profile data dirs

## License

MIT — see [LICENSE](LICENSE).
