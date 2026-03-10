# Sora Legacy Offline

Local, offline-first tooling for archiving your own Sora library into SQLite plus local image files.

## What It Does

- Authenticates with a real browser session.
- Scrolls a virtualized library UI and extracts prompt, task, and generation metadata.
- Downloads image assets to local storage.
- Stores task, generation, and asset records in SQLite.
- Supports delete-after-save workflows for users who want to drain the online library after local capture.
- Exports an offline gallery for browsing the archive locally.

## Why This Exists

Sora libraries are difficult to preserve cleanly because the UI is virtualized, items can disappear from the DOM as you scroll, and image metadata is scattered across the page state. This project focuses on a practical local-first workflow:

1. authenticate once,
2. capture metadata,
3. download assets,
4. verify results,
5. browse the archive offline.

## Features

- Python CLI built around Playwright
- SQLite-backed metadata storage
- Local image archiving
- Human-in-loop browser mode
- Delete-after-save support
- Offline gallery export

## Install

```bash
python -m venv .venv
.venv\Scripts\activate
pip install -e .
playwright install chromium
```

## Typical Commands

Auth:

```bash
python -m sora_legacy.cli auth --base-url "https://sora.chatgpt.com/library?type=images" --headful --browser-channel chrome
```

Sync:

```bash
python -m sora_legacy.cli sync --base-url "https://sora.chatgpt.com/library?type=images" --db data\archive.sqlite --route task_details --headful --human-in-loop --browser-channel chrome --min-delay-ms 500 --max-delay-ms 500 --item-delay-ms 500 --download-images --delete-after-save
```

Verify:

```bash
python -m sora_legacy.cli verify --db data\archive.sqlite
```

Export gallery:

```bash
python -m sora_legacy.cli export --db data\archive.sqlite --format gallery --out exports
```

## Notes

- This project is intended for personal archiving of your own library.
- Browser authentication stays local on disk.
- Generated assets, databases, and logs should not be committed to a public repo.
- The live Sora UI is virtualized, so collector behavior is designed around incremental DOM recovery rather than static page scraping.

## Repo Layout

- `sora_legacy/`: core CLI, collectors, storage, and export logic
- `scripts/build_public_release.ps1`: creates a sanitized shareable folder
- `PUBLIC_SHARE.md`: reusable public-facing copy

## X Post

Built an offline-first Sora archiver in Python + Playwright.

It logs into a real browser session, scrolls the virtualized library UI, stores task/generation metadata in SQLite, downloads image assets locally, and can export an offline gallery.

Interesting part was handling a DOM that recycles items while scrolling.

## Reddit Post

I built a local-first Sora library archiver and offline gallery exporter.

The tool uses Playwright for auth + collection, SQLite for task/generation/asset metadata, and a local file layout for downloaded images. One of the harder parts was dealing with the virtualized library UI where cards disappear from the DOM as you scroll, which makes naive scraping unreliable.

Current workflow:

- authenticate in a real browser
- sync library metadata and images
- optionally delete after save
- verify the archive
- export a browsable offline gallery

Stack:

- Python 3.11+
- Playwright
- SQLite

If people are interested, I can share more details about the collector strategy and the tradeoffs around delete-after-save vs. accepting occasional orphans.

## TikTok Caption

Built a Sora offline archiver.
Python + Playwright + SQLite.
It logs in, captures prompts/tasks/generations, downloads images, and builds an offline gallery.
The hardest part was scraping a virtualized UI that keeps recycling DOM items while scrolling.

## Public Release

To build a sanitized folder for sharing:

```powershell
.\scripts\build_public_release.ps1
```

That creates `_public_release\` with source code and public docs, while excluding local data, logs, assets, auth state, worktrees, and Codex notes.
