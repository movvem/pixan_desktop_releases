# Pixan desktop releases

Public downloads for the Pixan desktop app: its signed VM images, its installers and its update feed. This repo holds no code, only GitHub releases; the files are release assets, never commits.

## What a release holds

Release `vX.Y.Z`:

- The VM image (amd64): `manifest.json`, `manifest.sig`, `base.qcow2.zst`, `vmlinuz.zst` and `initrd.img.zst`. The image version is `X.Y.Z`, the same as the app's.
- The installers: Windows `Pixan_X.Y.Z_x64-setup.exe` with its updater signature `.sig`, and Linux `.deb` and `.rpm`.
- The update feed `latest.json`, pointing at this release's installers.
- The exact source of the QEMU bundled in the Windows installer. QEMU is GPL, so its source ships wherever the binary does.

Every release carries all of these, so the latest published release serves the image, the installers and the feed together.

## How the app reaches them

The app has pixan-owned URLs built in, never this repo's. They redirect to the latest release here:

| Built into the app | Redirects to |
|---|---|
| `https://images.pixan.co/stable/<file>` | `https://github.com/movvem/pixan_desktop_releases/releases/latest/download/<file>` |
| `https://updates.pixan.co/latest.json` | `https://github.com/movvem/pixan_desktop_releases/releases/latest/download/latest.json` |

- A release becomes `latest` only once all its files are up, so the switch is atomic. Each release keeps its own URLs: a download in progress is never cut by the next release.
- The app follows redirects and resumes interrupted downloads with HTTP Range requests.

## Trust

This host is not trusted, and does not need to be.

- The app checks `manifest.sig` (Ed25519) against image keys built into it, then each file's SHA-256 from the manifest. It verifies the installed image again before every boot.
- Installer updates are checked against the updater key built into the app.
- A tampered or truncated file is refused.

## Pre-releases

Pre-releases are test builds, never meant for users. `latest` skips them, so the app's URLs never serve one.
