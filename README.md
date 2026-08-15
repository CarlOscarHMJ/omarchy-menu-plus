# Omarchy Menu Plus

<video src="https://github.com/CarlOscarHMJ/omarchy-menu-plus/raw/master/demo.mp4" controls muted></video>

A drop-in replacement for [Omarchy](https://github.com/basecamp/omarchy)'s
built-in `Super + Space` menu (`omarchy.menu`) that adds three search modes
lost when Omarchy 4.0 ("quattro") replaced `walker`/`elephant`:

- **`.query`** — live, whole-filesystem file search (via `locate`), opening
  the selected file with its default app.
- **`=expr`** — an inline calculator (`+ - * / % ^`, plus `sqrt`, `pow`,
  `sin`/`cos`/`tan`, `log`, `round`, `pi`, `e`, and more), copying the
  result to your clipboard on Enter.
- **`!amount currency to currency`** — a live currency converter (e.g.
  `!420 usd to dkk`, `!$420 to dkk`, `!420 to £`), copying the result to
  your clipboard on Enter.

Everything else about the menu — apps, settings, power, fonts, etc. — is
completely unchanged; this is the stock `omarchy.menu` source with the three
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
| `!420 usd to dkk` | `2714.46 DKK` |
| `!$420 to dkk` | `2714.46 DKK` (symbols work on either side, and as the target too) |
| `!100 EUR to £` | `85.45 GBP` |

Currency codes and symbols are case-insensitive and recognized in any
position (`usd 420 to dkk` works too). Supported symbols: `$` (USD), `£`
(GBP), `€` (EUR), `¥` (JPY), `₹` (INR), `₩` (KRW) — anything else, use the
3-letter ISO code.

File search results open with the file's default app on Enter. Calculator
and currency results copy to your clipboard on Enter.

## Dependencies

- **`plocate`** — required for file search. Not installed by default on
  Omarchy; install and enable its index updater:
  ```
  sudo pacman -S plocate
  sudo systemctl enable --now plocate-updatedb.timer
  ```
  Without it, `.query` search will just return no results (nothing breaks,
  it silently finds nothing).
- `gtk-launch`, `xdg-terminal-exec`, `xdg-mime`, `wl-copy`, `curl` — all
  standard on an Omarchy install already, no extra setup needed.
- **Network access** — currency conversion calls the free
  [Frankfurter API](https://frankfurter.dev/) (ECB reference rates, no API
  key required). Offline, or if the request fails, `!query` just returns no
  result.

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
