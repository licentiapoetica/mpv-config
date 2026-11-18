# mpv-config

My personal configuration for [mpv](https://mpv.io): a small always-on-top player setup with mouse-wheel seeking, YouTube playback through `yt-dlp`, a playlist manager, and a clip/WebM encoder.

## Requirements

- [mpv](https://mpv.io)
- [yt-dlp](https://github.com/yt-dlp/yt-dlp), for playing URLs (YouTube etc.)

## Installation

mpv reads its config from `~/.config/mpv` on Linux (`%APPDATA%\mpv` on Windows). Back up any existing config first, then either clone straight into place:

```sh
git clone <repo-url> ~/.config/mpv
```

or keep the repo elsewhere and symlink it:

```sh
ln -s ~/Projects/mpv-config ~/.config/mpv
```

## What's included

```
.
├── mpv.conf                  # player options
├── input.conf                # custom key/mouse bindings
├── scripts/
│   ├── freezer.lua           # keeps the window geometry in sync with its size
│   ├── playlistmanager.lua   # on-screen playlist manager
│   └── webm.lua              # cut and encode clips to WebM
└── webm.lua                  # older copy of webm.lua, not loaded by mpv
```

### `mpv.conf`

| Option | Effect |
| --- | --- |
| `profile=gpu-hq` | Higher-quality scaling and rendering |
| `ontop` | Window stays above other windows |
| `loop-file=inf` | Loops the current file forever |
| `no-keepaspect-window` | Window can be resized freely, regardless of video aspect ratio |
| `volume=50` | Starts at 50% volume |
| `window-scale=1`, `geometry=50%:50%` | Opens at native size, centered on screen |
| `ytdl_hook-ytdl_path=yt-dlp` | Uses `yt-dlp` instead of `youtube-dl` |
| `ytdl-format=...` | Prefers H.264 MP4 video up to 1080p plus best audio |

### Key bindings (`input.conf`)

| Input | Action |
| --- | --- |
| Mouse wheel up | Seek forward 10 s |
| Mouse wheel down | Seek back 10 s |
| Middle click | Toggle mute |
| `k` | Shuffle the playlist |

All other keys use [mpv's defaults](https://mpv.io/manual/stable/#keyboard-control).

## Scripts

### playlistmanager.lua

From [jonniek/mpv-playlistmanager](https://github.com/jonniek/mpv-playlistmanager). Displays and edits the playlist on screen.

| Key | Action |
| --- | --- |
| `Shift+Enter` | Show/hide playlist |
| `Up` / `Down`, `PgUp` / `PgDn`, `Home` / `End` | Move the cursor |
| `Left` / `Right` | Select a file to move it |
| `Enter` | Play the file under the cursor |
| `Backspace` | Remove the file from the playlist |
| `Esc` | Close the playlist |

Settings are at the top of the script, or can be overridden in `script-opts/playlistmanager.conf`.

### webm.lua

From [ekisu/mpv-webm](https://github.com/ekisu/mpv-webm). Cuts a section of the current video and encodes it, using mpv itself as the encoder.

Press `Shift+W` to open the menu, then:

| Key | Action |
| --- | --- |
| `1` / `2` | Set start / end time |
| `c` | Crop (`1` / `2` set corners, `r` resets, `Enter` confirms) |
| `o` | Encoding options |
| `p` | Preview the clip |
| `e` | Encode |
| `Esc` | Close the menu |

Settings changed from the upstream defaults:

- **Output folder:** `~/Videos/mpv-encodes` (change `output_directory` in [scripts/webm.lua](scripts/webm.lua) to use another folder)
- **File name:** `<title>_<start>-<end>`
- **Target size:** about 9.5 MB, VP8 WebM, CRF 7

This copy also adds a few on-screen tweaks: a border around the video frame in crop mode, and outlined menu text.

### freezer.lua

Watches the window size and writes it back into mpv's `geometry` property, so a resized or maximized window keeps that size instead of snapping back when the next file loads. Originally written for a Windows issue where maximized windows "detach".
