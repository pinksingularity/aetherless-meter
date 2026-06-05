# Aetherless Meter

**A minimal FFXIV TUI meter by Pink Singularity / Nixie.**

A terminal combat meter for **IINACT / OverlayPlugin WebSocket** data.

[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/Z7J62034HA)

<img width="1023" height="369" alt="597725864-6208b44b-0ec6-4906-a684-6e7db8cb6f5a" src="https://github.com/user-attachments/assets/0f26d3d5-370f-4229-8bcb-805fd9bde060" />

Aetherless Meter is Linux-first and designed for terminal-based, lightweight, second-screen, or minimal setups.

The executable/script entry remains `ffxiv-tui` for clarity, while the installed launcher is `aetherless-meter`.

## Features

- Terminal UI powered by Rich
- DPS table and bars
- Optional HPS columns
- 24-player alliance mode
- Job-colored or plain bars
- Optional local Relative Score mode
- Privacy Mode for hiding names, WebSocket URL, and connection errors with terminal shade characters
- Automatic update notices from GitHub Releases
- Automatic reconnect
- Clean Ctrl+C shutdown
- CLI `--help` output always in English
- Multi-language TUI: English, Spanish, Chinese, French, German, Korean, Japanese
- Configurable host, port, path, or full WebSocket URL
- Installer with private virtual environment and global launcher

## Platform support

| Platform | Status | Notes |
|---|---|---|
| Linux | Tested / primary target | Recommended platform. Designed around terminal-first and lightweight setups. |
| Windows | Experimental / untested | The Python app may work through PowerShell or Windows Terminal, but the installer flow is Linux-oriented for now. |
| macOS | Untested | It may work in theory because the app is Python-based, but no macOS FFXIV setup has been tested. |

Aetherless Meter is mainly intended for people who want a terminal-based meter, a second-screen setup, or a lightweight Linux-friendly alternative.

## Requirements

- Python 3.10+
- IINACT or OverlayPlugin WebSocket Server enabled
- A terminal with Unicode support

## Install

Recommended install:

```bash
python ffxiv_tui.py --install
```

This creates a private virtual environment in:

```text
~/.local/share/aetherless-meter/venv
```

and installs a global launcher in:

```text
~/.local/bin/aetherless-meter
```

The installer tries to detect `bash`, `zsh`, or `fish` and adds `~/.local/bin` to the matching shell config automatically.

After installing, you can run it immediately with the full launcher path:

```bash
~/.local/bin/aetherless-meter
```

After restarting your terminal or reloading your shell config, run it from anywhere with:

```bash
aetherless-meter
```

Skip PATH/shell config changes:

```bash
python ffxiv_tui.py --install --no-modify-path
```

Uninstall:

```bash
aetherless-meter --uninstall
```

Manual development install:

```bash
python -m venv ~/.local/share/ffxiv-tui
source ~/.local/share/ffxiv-tui/bin/activate
pip install -r requirements.txt
```

For fish shell:

```fish
source ~/.local/share/ffxiv-tui/bin/activate.fish
```

## Quick start

Same machine as IINACT / OverlayPlugin:

```bash
aetherless-meter
```

Second PC/laptop over LAN:

```bash
aetherless-meter --host FFXIV_PC_IP
```

Replace `FFXIV_PC_IP` with the LAN IP of the machine running FFXIV and IINACT / OverlayPlugin.

Alliance raid mode:

```bash
aetherless-meter --host FFXIV_PC_IP --alliance --bar-mode job --bar-width 10
```

Plain bars:

```bash
aetherless-meter --host FFXIV_PC_IP --bar-mode plain
```

Show HPS:

```bash
aetherless-meter --host FFXIV_PC_IP --show-hps
```

## Update notice

Aetherless Meter checks GitHub Releases for updates by default and shows a high-contrast notice in the header when a newer version is available.

The TUI starts first; the update check runs in the background after a short delay.

Default update target:

```text
pinksingularity/aetherless-meter
```

Disable update checks:

```bash
aetherless-meter --no-check-updates
```

Use a different repo for update checks:

```bash
aetherless-meter --update-repo owner/repo
```

Tune update check timing:

```bash
aetherless-meter --update-delay 2 --update-timeout 1.5
```

## Privacy Mode

Hide names and potentially sensitive connection info using terminal shade characters.

Hide everyone:

```bash
aetherless-meter --privacy-mode all
```

Hide only yourself:

```bash
aetherless-meter --privacy-mode self
```

Hide everyone except yourself:

```bash
aetherless-meter --privacy-mode others
```

Mask styles:

```bash
aetherless-meter --privacy-mode all --privacy-style light
aetherless-meter --privacy-mode all --privacy-style medium
aetherless-meter --privacy-mode all --privacy-style heavy
aetherless-meter --privacy-mode all --privacy-style mixed
```

Styles use `░`, `▒`, `▓`, or a mixed pattern.

Privacy Mode can hide:

- player names
- detected player name in the header
- WebSocket URL
- connection error text

## Relative Score

Aetherless Meter includes an optional, local **Relative Score** mode.

This is **not** an FFLogs parse. It does not use FFLogs data, historical rankings, boss partitions, or global job statistics.

It only compares visible/current encounter DPS inside Aetherless Meter.

Examples:

```bash
aetherless-meter --score-mode overall
aetherless-meter --score-mode role
aetherless-meter --score-mode job
```

Modes:

- `off`: disabled
- `overall`: compare DPS against all visible combatants
- `role`: compare DPS against the same role group
- `job`: compare DPS against the same job, useful only if duplicates exist

Groups with fewer than two valid members show `-` instead of a misleading solo 100.

Relative Score uses FFLogs-inspired terminal colors as a visual aid, but it is still not an FFLogs parse.

## FFLogs / Archon note

Aetherless Meter does **not** upload logs to FFLogs and does **not** replace FFLogs Uploader or Archon.

It reads live WebSocket meter data for a lightweight terminal display. For official logs, parses, rankings, and post-pull analysis, use FFLogs / Archon tools.

## WebSocket options

Use a full WebSocket URL manually:

```bash
aetherless-meter --ws ws://FFXIV_PC_IP:10501/ws
```

If your IINACT build uses `/` instead of `/ws`:

```bash
aetherless-meter --host FFXIV_PC_IP --path /
```

## Languages

CLI `--help` output is always shown in English. The TUI language is still controlled by `--lang` and defaults to `auto`.

Default language mode is `auto`. It tries to use your system locale and falls back to English.

```bash
aetherless-meter --lang auto
```

Available language options:

```bash
aetherless-meter --lang en
aetherless-meter --lang es
aetherless-meter --lang zh
aetherless-meter --lang fr
aetherless-meter --lang de
aetherless-meter --lang ko
aetherless-meter --lang ja
```

## Resource usage

Aetherless Meter is designed to be lightweight. It does not store long combat history; it keeps only the current encounter state in memory.

In current Linux/Konsole testing, it used around **40 MiB RSS**, though this can vary depending on Python, terminal, system libraries, and setup.

For lower CPU usage, reduce the refresh rate:

```bash
aetherless-meter --refresh 2
```

## Versioning

Public releases use a simple `v1.0`, `v1.1`, `v1.2` style version scheme.

- Major milestone releases use `.0`
- Smaller feature/fix releases increase by `.1`

Current version: **v2.0**

## Development note

Parts of this project were created with AI assistance and reviewed/tested by the maintainer. The accuracy of languages other than Spanish and English may vary.

The maintainer is responsible for the code, behavior, packaging, and releases.


## Notes

v2.0 consolidates the changes made after v1.2 and documents the project as a more complete Linux-first terminal meter.

This project is unofficial and not affiliated with Square Enix, ACT, OverlayPlugin, IINACT, FFLogs, or Archon.

Use third-party tools responsibly and privately. Remember, these kinds of tools are meant to help you improve, not to yell at others. If that’s the case, please refrain from using this tool.

## FAQ: ?

Why this exists?

It's simple: I have a low-resolution monitor that doesn't let me use overlays in the game. To avoid having everything look cluttered, I decided to dust off a 16-year-old PC to display metrics, which saves me the hassle of a cluttered screen and gives that old machine a new lease on life.

## Tested on:


Arch Linux / Shell (TUI), i3 370M w/4gb RAM as second machine for showing metrics

CachyOS / Kitty, Ghostty, Konsole, Alacritty, Ryzen 3 3200G, 20GB RAM

(Windows compatibility have not been tested)
