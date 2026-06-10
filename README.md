# killer

A terminal UI for managing heavy applications on Linux. Toggle apps on/off, auto-detect all running processes, drop disk cache, reclaim swap, and nuke everything back to a clean desktop state.

![Python](https://img.shields.io/badge/python-3.6%2B-blue) ![Platform](https://img.shields.io/badge/platform-Linux-lightgrey) ![License](https://img.shields.io/badge/license-MIT-green)

## Features

- **Tab-based UI** — Browsers, Communication, Dev, Creative, Servers, Background, Other
- **Auto-detect** — scans all running user processes, not just a hardcoded list
- **Start & stop** — toggle known apps on/off with a single keypress
- **Drop cache** — free Linux page cache + reclaim swap in one step
- **Full reset** — kills everything except your desktop environment (requires `YES` confirmation)
- **RAM display** — live memory usage and bar graph per app

## Requirements

- Python 3.6+
- Linux (uses `/proc`, `ps`, `pgrep`, `pkill`)
- `curses` (included in Python stdlib)
- Optional: `flatpak` for Flatpak apps, `sudo` for system services

## Install

### One-liner

```bash
curl -fsSL https://raw.githubusercontent.com/Soab42/killer/main/killer \
  -o ~/.local/bin/killer && chmod +x ~/.local/bin/killer
```

### Manual

```bash
git clone git@github.com:Soab42/killer.git
cd killer
cp killer ~/.local/bin/killer
chmod +x ~/.local/bin/killer
```

Make sure `~/.local/bin` is in your `PATH`. Add this to your `~/.bashrc` or `~/.zshrc` if it isn't:

```bash
export PATH="$HOME/.local/bin:$PATH"
```

Then reload your shell:

```bash
source ~/.bashrc   # or source ~/.zshrc
```

Now run from anywhere:

```bash
killer
```

## Keybindings

| Key | Action |
|-----|--------|
| `/` | Open search — type to filter across all apps |
| `Esc` | Clear search / cancel number input |
| `↑` / `↓` | Navigate the app list |
| `Enter` / `Space` | Toggle selected app (start or stop) |
| `←` / `→` or `Tab` | Switch category tab |
| `1`–`9`, `10`, `11`... | Type a number then Enter to jump and toggle |
| `Backspace` | Delete last search character or digit |
| `a` | Stop all running apps in the current tab |
| `c` | Drop page cache + reclaim swap (`sudo` required) |
| `X` | **Full reset** — kills everything, leaves only the desktop |
| `r` | Refresh now |
| `q` | Quit |

### Search

Press `/` to enter search mode. Type any part of an app name or category.
Results are filtered live across **all** categories. Each result shows a
`[Cat]` badge so you know where it lives. Press `Enter` to toggle, `Esc` to clear.

## Tab Overview

| Tab | Contents |
|-----|----------|
| **All** | Every app across all categories |
| **Browsers** | Chrome, Firefox, and detected browsers |
| **Communication** | Discord, Teams, Zoom, Telegram, Thunderbird, BlueMail |
| **Dev** | Postman, Zed, and detected dev tools |
| **Creative** | OBS, GIMP, Kdenlive, LibreOffice, VLC |
| **Servers** | Nginx, Apache2, MySQL, PHP-FPM, PHP Dev Server |
| **Background** | Dropbox, Claude Desktop, Alpaca, Standard Notes |
| **Other** | Any running user process not matched above |

Apps prefixed with `~` are **auto-detected** (kill only — no start command known).

## RAM display

The title bar shows real available memory, not the misleading "used" figure:

```
RAM 45%  8200 MB free / 15700 MB
```

Linux fills free RAM with disk cache. The percentage here reflects what is
actually committed to processes, so 45% means 55% is genuinely free.

## Full Reset

Press `X` to open the confirmation dialog. Type `YES` and press Enter.

This will:
1. `systemctl stop` all managed services (MySQL, Nginx, Apache, PHP-FPM)
2. Kill all known app processes
3. Scan and kill remaining user processes not in the system whitelist
4. Drop page cache and reclaim swap

**Kept alive:** `cosmic-*`, `pipewire`, `wireplumber`, `dbus`, `NetworkManager`,
`Xwayland`, `systemd`, `polkit`, `gnome-keyring`, your terminal and shell.

## Customising

Edit the `APPS` list near the top of the script to add your own apps:

```python
{"name": "MyApp",  "cat": "Dev",
 "match": "myapp",                        # string matched against ps args
 "kill": ["pkill", "-f", "myapp"],
 "start": "myapp --some-flag"},
```

For Flatpak apps:

```python
{"name": "MyFlatpak",  "cat": "Communication",
 "match": "com.example.MyApp",
 "kill": ["flatpak", "kill", "com.example.MyApp"],
 "start": "flatpak run com.example.MyApp"},
```

For systemd services:

```python
{"name": "Redis",  "cat": "Servers",
 "match": "redis-server",
 "kill": ["sudo", "systemctl", "stop", "redis"],
 "start": "sudo systemctl start redis",
 "service": True, "service_name": "redis"},
```

## License

MIT
