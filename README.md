# Omarchy Menu Plus

A drop-in replacement for [Omarchy](https://github.com/basecamp/omarchy)'s
built-in `Super + Space` menu (`omarchy.menu`) that adds two search modes
lost when Omarchy 4.0 ("quattro") replaced `walker`/`elephant`:

- **`.query`** — live, whole-filesystem file search (via `locate`), opening
  the selected file with its default app.
- **`=expr`** — an inline calculator (`+ - * / % ^`, plus `sqrt`, `pow`,
  `sin`/`cos`/`tan`, `log`, `round`, `pi`, `e`, and more), copying the
  result to your clipboard on Enter.

Everything else about the menu — apps, settings, power, fonts, etc. — is
completely unchanged; this is the stock `omarchy.menu` source with the two
modes layered on top.

## Install

```
omarchy plugin add https://github.com/CarlOscarHMJ/omarchy-menu-plus
```

**This replaces your default Omarchy menu.** Enabling this plugin uses the
same `clonedFrom` mechanism as `omarchy plugin clone` — it automatically
disables the stock `omarchy.menu` and repoints `Super + Space`, the taskbar
menu button, and the `omarchy-menu` CLI at this plugin instead. No keybinding
or config changes are needed; it "just works" as a transparent replacement.

## Remove

```
omarchy plugin remove io.github.carloscarhmj.omarchy-menu-plus
```

Removing (or disabling) it automatically restores the stock `omarchy.menu`.

## Usage

| Type | Result |
|---|---|
| `firefox` | normal app/menu search, unchanged |
| `.bashrc` | live file search — every file whose name contains "bashrc" |
| `.report*pdf` | wildcard search — files containing **both** "report" and "pdf" anywhere in the name |
| `=4*23` | `92` |
| `=sqrt(16)` | `4` |
| `=pow(2,10)+round(pi)` | `1027` |

File search results open with the file's default app on Enter. Calculator
results copy to your clipboard on Enter.

## Dependencies

- **`plocate`** — required for file search. Not installed by default on
  Omarchy; install and enable its index updater:
  ```
  sudo pacman -S plocate
  sudo systemctl enable --now plocate-updatedb.timer
  ```
  Without it, `.query` search will just return no results (nothing breaks,
  it silently finds nothing).
- `gtk-launch`, `xdg-terminal-exec`, `xdg-mime`, `wl-copy` — all standard on
  an Omarchy install already, no extra setup needed.

## Why this exists / how file-open works

On at least some setups, plain `xdg-open` hangs indefinitely when called
from a detached process with no window context (it goes through
`xdg-desktop-portal`, which needs one). File search results are opened via
the bundled [`scripts/smart-open.sh`](scripts/smart-open.sh) instead, which
resolves the `.desktop` file via `gtk-launch` directly without touching the
portal. Terminal-based default apps (e.g. `nvim` as the default text editor)
are additionally wrapped in `xdg-terminal-exec` so they get a real TTY.

## Credit / license

This is a fork of Omarchy's built-in `omarchy.menu` plugin
(`shell/plugins/menu/` in [basecamp/omarchy](https://github.com/basecamp/omarchy),
MIT licensed) with the search modes above added. See [LICENSE](LICENSE).
