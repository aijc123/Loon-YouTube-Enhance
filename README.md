# Loon YouTube Enhance

**English** | [简体中文](./README.zh-CN.md)

A Loon-focused YouTube / YouTube Music enhancement plugin using a pinned, device-tested Loon playback-ad path plus Maasea's player/settings enhancements.

## Goals

- Remove newer watch-page/feed Sponsored cards in addition to normal video/feed/search ads.
- Handle encrypted `initplayback` responses directly so background playback and ad filtering are not dependent on the older request-only path.
- Remove YouTube video, feed, search and Shorts ads.
- Keep background playback enabled.
- Use YouTube/iOS native Picture in Picture instead of force-injecting PiP capability, to reduce the black-screen / stalled-PiP behavior reported with newer YouTube builds.
- Block YouTube-related UDP/QUIC so requests fall back to TCP/HTTPS and remain visible to Loon MITM/scripts.
- Offer selectable subtitle translation languages.
- Keep a stable raw subscription URL for Loon updates.

## Why this fork exists

Recent YouTube/iOS versions changed playback and PiP behavior. Maasea's current Enhance module uses the newer `initplayback` + `log_event` flow and remains the upstream base here.

This fork adds three Loon-specific changes:

1. **YouTube UDP/QUIC fallback**
   - `youtube.com`
   - `googlevideo.com`
   - `youtubei.googleapis.com`

   UDP is rejected only for those YouTube domains, forcing TCP/HTTPS fallback instead of globally disabling UDP/443.

2. **Native PiP compatibility patch**
   - Maasea's response script enables both PiP and background playback capabilities.
   - This fork removes only the forced PiP capability injection while keeping background playback enabled.
   - The intent is to let current iOS/YouTube use native PiP, avoiding a known class of PiP black-screen / stalled playback issues.

3. **Current initplayback hotfix**
   - Some current YouTube `initplayback` URLs no longer include the older `&ack` marker used by earlier match rules.
   - This fork matches all `googlevideo.com/initplayback` requests so the current request handler is not bypassed.

## Loon subscription

```
https://raw.githubusercontent.com/aijc123/Loon-YouTube-Enhance/main/YouTube_Enhance.lpx
```

Add that URL as a Loon plugin subscription. Future updates keep the same URL.

## Install / upgrade

1. Disable other YouTube ad-block / Enhance plugins first.
2. Remove old manual YouTube QUIC local rules to avoid duplicate rules.
3. Add the raw subscription URL above.
4. Enable MitM and trust the Loon certificate.
5. Force-quit YouTube once after changing plugin versions, then reopen it.

## Included settings

- Hide Upload button
- Hide Shorts button
- Hide YouTube Music immersive/segment button
- Subtitle translation language
- Debug mode

## Upstream

- Maasea/sgmodule
- Uses current Maasea request logic for `initplayback` and `log_event`.
- Response script is synced from Maasea and patched only to stop forcing PiP capability while keeping background playback.

## Known limitations

YouTube changes server-side behavior frequently. No ad-blocking method can be guaranteed to remain perfect indefinitely. If an ad leaks or playback stalls, capture Loon Requests while the problem is happening before restarting YouTube; that makes the failing endpoint visible.

This project is an unofficial compatibility fork and is not affiliated with YouTube, Google, Loon, or Maasea.


## Core provenance and stability policy

The working pre-roll/mid-roll removal path is intentionally **not original code from this repository**.

### teaoea/shell — MIT

- Upstream: https://github.com/teaoea/shell
- Pinned source commit: `a44ce03f12368d44f4a324eb2c0c18c00d60e937`
- Vendored copies:
  - `vendor/teaoea/request.min.js`
  - `vendor/teaoea/response.min.js`
- Used for:
  - `player/get_watch` request sanitization
  - `player/ad_break` handling
  - `browse/next/search` Sponsored-card filtering
- License and attribution are preserved in `vendor/teaoea/NOTICE.md`.

### Maasea/sgmodule

- Upstream: https://github.com/Maasea/sgmodule
- Pinned local copies:
  - `scripts/stable/youtube.response.js`
  - `scripts/stable/youtube.request.js`
- Used for background-play capability, subtitle translation and selected UI enhancements.

### This repository's Loon glue

`YouTube_Enhance.lpx` combines the pinned upstream logic with Loon-specific behavior:
- YouTube-only QUIC fallback
- MitM hostnames
- `initplayback -> reject_video(200)` fallback
- tracking fallbacks

### Stable-core policy

The current core has been device-tested with pre-roll removal and background playback working. Therefore:

- Do **not** routinely modify or auto-sync the pinned teaoea core.
- Do **not** replace the working `initplayback -> reject_video(200)` path just because another upstream implementation is newer.
- Prefer adding new features outside the playback/ad core.
- Change the core only when a reproducible YouTube update breaks it and Loon request logs identify the failing path.
- Archive the last working plugin in `legacy/` before every core migration.

In short: **working core is pinned, not rolling.**
