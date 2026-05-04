<div align="center">

# PullTube

**Pull any video. No fuss.**

*Paste a link · pull the video · move on with your day.*

[![macOS](https://img.shields.io/badge/macOS-13%2B-black?style=flat-square&logo=apple&logoColor=white)](https://github.com/zabrodsk/pulltube/releases)
[![Swift](https://img.shields.io/badge/Swift-5.9-F05138?style=flat-square&logo=swift&logoColor=white)](https://swift.org)
[![yt-dlp](https://img.shields.io/badge/yt--dlp-2026.03.17-red?style=flat-square)](https://github.com/yt-dlp/yt-dlp)
[![License](https://img.shields.io/badge/license-MIT-green?style=flat-square)](LICENSE)

[**pulltube.pages.dev**](https://pulltube.pages.dev)

</div>

---

## What it does

PullTube is a native macOS video downloader. Paste any YouTube, Vimeo, TikTok, or one of 1000+ other supported URLs — PullTube fetches the title and thumbnail, lets you pick a quality (4K if available), and downloads it straight to `~/Movies/PullTube`.

No terminal. No Homebrew. No Python environment. Just open the app.

## Features

- **Zero install** — `yt-dlp` and `ffmpeg` ship inside the app bundle. Nothing else needed.
- **Preview before download** — title and thumbnail appear before the download starts.
- **4K-aware format picker** — shows 4K only when the source video actually has it.
- **Every format** — MP4 (4K / 1080p / 720p / 480p), M4A audio, MP3 192k.
- **Batch queue** — drop multiple links or paste a list; they queue and download one by one.
- **Retry on failure** — failed rows show the error inline with a one-click retry.
- **Browser cookies** — toggle "Use Safari cookies" in Settings to bypass age gates and paywalls.
- **Native SwiftUI** — respects your system appearance (light / dark), no Electron, no web view.

## Supported sites

YouTube · Vimeo · TikTok · Twitch · Instagram · SoundCloud · Dailymotion · Reddit · Twitter/X · Bilibili · and **1000+ more** via yt-dlp.

## Requirements

| Requirement | Version |
|---|---|
| macOS | 13 Ventura or later |
| Architecture | Apple Silicon & Intel (universal) |

## Build with an AI agent

Paste the following prompt into [Claude](https://claude.ai/claude-code) (or any AI coding agent) and it will set up and run PullTube from scratch on your Mac:

---

```
You are setting up PullTube, a native macOS video downloader built with SwiftUI.

Project overview:
- Language: Swift 5.9, SwiftUI, macOS 13+
- Build system: XcodeGen (generates PullTube.xcodeproj from project.yml)
- Bundled binaries: yt-dlp (universal binary) + ffmpeg with dylib tree, all inside the app bundle
- No App Store, no code signing required — uses ad-hoc signing (CODE_SIGN_IDENTITY: "-")

Your job:
1. Check that the following are installed: Xcode (15+), xcodegen, Homebrew, ffmpeg
   - Install any missing tools: `brew install xcodegen ffmpeg`

2. Run the dependency download script to bundle yt-dlp and ffmpeg into the project:
   ./scripts/download-deps.sh
   This script:
   - Downloads the official yt-dlp universal binary from GitHub releases into Sources/PullTube/Resources/Vendored/
   - Copies ffmpeg from Homebrew, recursively bundles all non-system dylibs into Vendored/lib/
   - Rewrites dylib load paths to @executable_path/lib/ using install_name_tool

3. Generate the Xcode project:
   xcodegen generate

4. Build the app from the command line:
   xcodebuild -scheme PullTube -configuration Debug build

5. Launch the built app:
   open ~/Library/Developer/Xcode/DerivedData/PullTube-*/Build/Products/Debug/PullTube.app

Known issues to watch for:
- The post-build script copies Vendored/ using `ditto`, not `rsync` — rsync gets SIGKILL on iCloud Drive paths in Xcode's sandbox
- ffmpeg dylibs must all have @executable_path/lib/ load paths or the app will crash on launch
- If yt-dlp returns HTTP 403 on YouTube, enable "Use browser cookies" in Settings to use Safari's session

Once the app launches: paste any YouTube, Vimeo, or TikTok URL into the input bar, pick a quality (4K if available), and tap Download.
```

---

## Building from source

```bash
# 1. Clone
git clone https://github.com/zabrodsk/pulltube.git
cd pulltube

# 2. Download bundled binaries (yt-dlp + ffmpeg)
cd ../pulltube-macos
./scripts/download-deps.sh

# 3. Generate Xcode project
xcodegen generate

# 4. Open and build
open PullTube.xcodeproj
```

> The app uses ad-hoc code signing (`CODE_SIGN_IDENTITY: "-"`) so no Apple Developer account is required to build locally.

## Tech stack

| Layer | Technology |
|---|---|
| UI | SwiftUI (macOS 13+) |
| Download engine | yt-dlp 2026.03.17 (bundled universal binary) |
| Muxing | ffmpeg with 16 bundled dylibs, `@executable_path/lib/` rpath |
| Project config | XcodeGen |
| Dependency bundling | Custom `scripts/download-deps.sh` via `otool -L` + `install_name_tool` |

## How the bundling works

`scripts/download-deps.sh` downloads the official yt-dlp universal binary from GitHub releases and copies ffmpeg from Homebrew. It then recursively traverses ffmpeg's dylib dependencies with `otool -L`, copies all non-system libraries into `Vendored/lib/`, and rewrites their load paths to `@executable_path/lib/<name>` using `install_name_tool`. The Xcode post-build script uses `ditto` (not `rsync`) to copy the `Vendored/` folder into the app bundle — `ditto` handles iCloud Drive paths correctly in the Xcode build sandbox.

## License

MIT — see [LICENSE](LICENSE).

---

<div align="center">
Made with SwiftUI on a Mac.
</div>
