# Aetherless Meter

**A minimal FFXIV TUI meter by Pink Singularity / Nixie.**

<img width="1024" height="447" alt="image" src="https://github.com/user-attachments/assets/9c5f3766-c664-4d25-9db1-57689734a99b" />

A terminal combat meter for **IINACT / OverlayPlugin WebSocket** data.

It can run on the same machine as FFXIV, or on a second lightweight Linux laptop/mini PC over LAN.

The executable is `ffxiv-tui` for clarity and easy discovery.

## Features

- Terminal UI powered by Rich
- DPS table and bars
- Optional HPS columns
- 24-player alliance mode
- Job-colored or plain bars
- Privacy Mode for hiding names, WebSocket URL, and connection errors with terminal shade characters
- Automatic update notice from GitHub Releases
- Automatic reconnect
- Clean Ctrl+C shutdown
- Multi-language UI: English, Spanish, Chinese, French, German, Korean, Japanese
- Configurable host, port, path, or full WebSocket URL

## Requirements

- Python 3.10+
- IINACT or OverlayPlugin WebSocket Server enabled
- A terminal with Unicode support

## Install

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
./ffxiv-tui
```

Second PC/laptop over LAN:

```bash
./ffxiv-tui --host FFXIV_PC_IP
```

Replace `FFXIV_PC_IP` with the LAN IP of the machine running FFXIV and IINACT / OverlayPlugin.

Alliance raid mode:

```bash
./ffxiv-tui --host FFXIV_PC_IP --alliance --bar-mode job --bar-width 10
```

Plain bars:

```bash
./ffxiv-tui --host FFXIV_PC_IP --bar-mode plain
```

Show HPS:

```bash
./ffxiv-tui --host FFXIV_PC_IP --show-hps
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
./ffxiv-tui --no-check-updates
```

Use a different repo for update checks:

```bash
./ffxiv-tui --update-repo owner/repo
```

Tune update check timing:

```bash
./ffxiv-tui --update-delay 2 --update-timeout 1.5
```
## Privacy Mode

Hide names using terminal shade characters.

Hide everyone:

```bash
./ffxiv-tui --host FFXIV_PC_IP --privacy-mode all
```

Hide only yourself:

```bash
./ffxiv-tui --host FFXIV_PC_IP --privacy-mode self
```

Hide everyone except yourself:

```bash
./ffxiv-tui --host FFXIV_PC_IP --privacy-mode others
```

Mask styles:

```bash
./ffxiv-tui --privacy-mode all --privacy-style light
./ffxiv-tui --privacy-mode all --privacy-style medium
./ffxiv-tui --privacy-mode all --privacy-style heavy
./ffxiv-tui --privacy-mode all --privacy-style mixed
```

Styles use `░`, `▒`, `▓`, or a mixed pattern.

## WebSocket options

Use a full WebSocket URL manually:

```bash
./ffxiv-tui --ws ws://FFXIV_PC_IP:10501/ws
```

If your IINACT build uses `/` instead of `/ws`:

```bash
./ffxiv-tui --host FFXIV_PC_IP --path /
```

## Languages

CLI `--help` output is always shown in English. The TUI language is still controlled by `--lang` and defaults to `auto`.



Default language mode is `auto`. It tries to use your system locale and falls back to English.

```bash
./ffxiv-tui --lang auto
```

Available language options:

```bash
./ffxiv-tui --lang en
./ffxiv-tui --lang es
./ffxiv-tui --lang zh
./ffxiv-tui --lang fr
./ffxiv-tui --lang de
./ffxiv-tui --lang ko
./ffxiv-tui --lang ja
```

## IINACT / OverlayPlugin setup

For same-machine usage, the default should usually work:

```bash
./ffxiv-tui
```

For a second PC/laptop:

1. Enable the WebSocket Server in IINACT / OverlayPlugin.
2. Set the listen/bind IP to `0.0.0.0`.
3. Keep port `10501`, unless you changed it.
4. Connect from the second machine using the FFXIV machine's LAN IP:

```bash
./ffxiv-tui --host FFXIV_PC_IP
```

Do not expose this WebSocket port to the internet. Use it only on your local network.

## Versioning

Public releases use a simple `v1.0`, `v1.1`, `v1.2` style version scheme.

- Major milestone releases use `.0`
- Smaller feature/fix releases increase by `.1`

Current version: **v1.1**

## Development note

Parts of this project were created with AI assistance and reviewed/tested by the maintainer. The accuracy of languages other than Spanish and English may vary.

The maintainer is responsible for the code, behavior, packaging, and releases.

## Notes

This project is unofficial and not affiliated with Square Enix, ACT, OverlayPlugin, or IINACT.

Use third-party tools responsibly and privately. Remember, these kinds of tools are meant to help you improve, not to yell at others. If that’s the case, please refrain from using this tool.

## FAQ: ?

Why this exists?

It's simple: I have a low-resolution monitor that doesn't let me use overlays in the game. To avoid having everything look cluttered, I decided to dust off a 16-year-old PC to display metrics, which saves me the hassle of a cluttered screen and gives that old machine a new lease on life.

## Tested on:


Arch Linux / Shell (TUI), i3 370M w/4gb RAM as second machine for showing metrics

CachyOS / Kitty, Ghostty, Konsole, Alacritty, Ryzen 3 3200G, 20GB RAM

(Windows compatibility have not been tested)
