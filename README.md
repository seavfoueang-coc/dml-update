# DML — DramaBox Downloader

A Windows desktop app for downloading short-form drama episodes from a wide
range of streaming platforms. Paste a series URL, pick the episodes you want,
click download. Auto-merges segments, auto-converts TS→MP4, optional zip.

**Current version:** 2.4.1 — see [CHANGELOG.md](CHANGELOG.md) for full history.

---

## Quick start (end users)

1. Download `DML_v2.4.1_Setup.exe` from the
   [latest release](https://github.com/seavfoueang-coc/dml-update/releases/latest).
2. Run the installer (double-click). Old versions are upgraded in place.
3. Launch **DML Downloader** from your Start menu.
4. Pick **Classic** UI when the launcher dialog appears.
5. Paste a drama URL into the **DML** tab → click **Fetch Series**.
6. Tick the episodes you want → click **Bypass Download**.
7. Files land under `Downloads/DML_DL/<PLATFORM>/<Title>/Ep01.mp4`, etc.

The app updates itself: when a new version is released, you get a prompt on
launch and one click does the rest.

## Supported platforms

DML auto-detects the platform from the URL you paste — no need to pick a
platform manually.

### Drama / short-video apps (with full bypass where shipped)

| Platform | Notes |
|---|---|
| **DramaBox** | Full bypass; default if no other platform matches |
| **FlickReels** | Full bypass via bundled SpyderProxy + ad-reward unlocks. Asks Fast (parallel) vs Slow (sequential) at fetch time |
| **AnyReel** | Full bypass |
| **GoodShort** | Free episodes; locked are server-gated |
| **MyDrama**, **ShortsDrama** | HLS direct |
| **DramaBite**, **DramaReels**, **DramaShorts** | HLS direct |
| **MicroDrama**, **NetShort**, **ReelShort** | HLS direct |
| **ShortMovs**, **NovelQuick**, **TopShort** | HLS direct |
| **RushTV**, **Velolo**, **StardustTV**, **BiliTV** | HLS direct |
| **ShortTV** | Free episodes; locked are server-gated |
| **WeTV** | Cookie-based (paste browser cookie header) |
| **Aniwaves** | Anime catalog browser |

### General-purpose video sites

| Platform | Notes |
|---|---|
| **YouTube** | Single videos, playlists, channels (uses bundled yt-dlp) |
| **TikTok** | Single video or full profile |
| **Facebook** | Single reel or full profile (cookies for full-profile) |
| **Dailymotion** | Single video or full channel |
| **Pinterest**, **RedNote** (Xiaohongshu), **Douyin** | Each has its own tab |

## Features

- **Auto TS→MP4** — toggleable; remuxes downloaded `.ts` files into `.mp4` with no quality loss (ffmpeg `-c copy`).
- **Auto Zip** — toggleable; bundles the entire drama folder into a `.zip` after the last episode finishes. Runs in the background, UI stays responsive.
- **Auto Telegram** — toggleable; uploads each finished MP4 to your bot/chat (file ≤ 50 MB).
- **Network-recovery wait** — toggleable; if the connection drops mid-download, DML waits for it to come back instead of failing.
- **Browse picker** — for platforms with catalog support (Aniwaves, FlickReels), browse a poster grid instead of pasting URLs.
- **Episode merge** — Tools menu lets you concatenate any folder of `Ep01.mp4`-style files into one combined MP4.
- **History** — every successful download lands in the History tab with poster, source URL, and saved path.
- **Two UIs** — Classic (full-featured tabs) and Simple (minimal one-screen). Pick at launch or switch via the View menu.
- **Khmer language support** — bundled Noto Sans Khmer font; localised log messages.

## Requirements

- **Windows 10 / 11** (the installer is x64).
- ~600 MB disk space (the installer plus extracted runtime).
- Internet connection.
- Optional: a Telegram bot token + chat ID if you want auto-upload.

## Settings

Open via **Tools → Settings** (or the gear icon). Main toggles:

- **Network Recovery** — wait for internet to come back instead of failing.
- **Auto-zip drama folder after download**.
- **Confirm before download** (prompt before kicking off a multi-episode batch).
- **FlickReels Proxy** — leave blank to use the built-in default. Override with your own `http://user:pass@host:port` if needed.
- **Telegram Bot Token / Chat ID** — for auto-upload after each download.

Settings live in `~/.dramadl_settings.json`.

## Updating

DML checks for updates on every launch (silent, ~5s timeout). When a newer
version is available, an update dialog appears with the changelog and a
**Download & Install** button. The installer downloads in the background,
launches itself, and the new version replaces the old in place. Your settings
and history are preserved.

The version metadata is hosted at
`https://raw.githubusercontent.com/seavfoueang-coc/dml-update/refs/heads/main/version.json`.
The actual installer binaries are attached to GitHub releases on the same repo.

See [UPDATER.md](UPDATER.md) for the release-checklist used by maintainers.

---

# Developer guide

## Building from source

Requirements:

- Python 3.10 (3.11+ may work but is not the tested target).
- The `requirements.txt` packages: `pip install -r requirements.txt`.
- `yt-dlp.exe` and `ffmpeg.exe` in the project root (downloaded by hand or via your preferred method).
- For installer builds: [Inno Setup 6](https://jrsoftware.org/isinfo.php).

## Project layout

| Area | File(s) |
|------|---------|
| Entry point + Classic UI | `main.py` (`DramaBoxApp`, `UIChooserDialog`, `main()`) |
| Simple UI | `simple_ui.py` (`SimpleDownloaderWindow`, `DownloadManager`) |
| Platform dispatch | `platform_registry.py` |
| Updater | `updater.py`, `config/update_config.json`, `config/version.json` |
| Licensing / protection | `licensing/manager.py`, `core/protect.py`, `license_worker/worker.js` |
| Scrapers | `scrapers/*.py` (one per platform) |
| Tests | `tests/` (`pytest`, run with `QT_QPA_PLATFORM=offscreen`) |
| Build | `build/build_nuitka.bat`, `build/DramaDL.spec`, `build/installer.iss` |

## Other docs in this folder

- [CHANGELOG.md](CHANGELOG.md) — single source of truth for version history. **Always update when code changes.**
- [DUAL_UI.md](DUAL_UI.md) — Classic + Simple UIs, launch chooser, platform registry.
- [UPDATER.md](UPDATER.md) — in-app updater + release checklist.
- [PROTECTION.md](PROTECTION.md) — licensing / anti-piracy layers.
- [LICENSE_BOT.md](LICENSE_BOT.md) — Telegram license admin bot + worker admin API.
- [FACEBOOK_REELS.md](FACEBOOK_REELS.md) — scrape a Facebook profile's reels (with cookies for full-profile).
- [FLICKREEL_NOTES.md](FLICKREEL_NOTES.md) — FlickReels reverse-engineering reference (bypass shipped).

## Standing rule

The changelog lives **only** in `docx/CHANGELOG.md` — there is no root
`CHANGELOG.md`. Update `docx/CHANGELOG.md` (and any other affected `docx/`
files) on every code change, before committing.
