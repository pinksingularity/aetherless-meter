# Aetherless Meter

**A minimal FFXIV TUI meter by Pink Singularity / Nixie.**

A terminal combat meter for **IINACT / OverlayPlugin WebSocket** data.

It can run on the same machine as FFXIV, or on a second lightweight Linux laptop/mini PC over LAN.

The executable is `ffxiv-tui` for clarity and easy discovery.

## Features

- Terminal UI powered by Rich
- DPS table and bars
- Optional HPS columns
- 24-player alliance mode
- Job-colored or plain bars
- Automatic reconnect
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

Use a full WebSocket URL manually:

```bash
./ffxiv-tui --ws ws://FFXIV_PC_IP:10501/ws
```

## Languages

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
3. Set port `10501`
4. Connect from the second machine using the FFXIV machine's LAN IP:

```bash
./ffxiv-tui --host FFXIV_PC_IP
```

Do not expose this WebSocket port to the internet. Use it only on your local network.

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
