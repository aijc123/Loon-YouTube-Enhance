# Changelog

## 2026-10-05 — Sponsored-ad + background-play hotfix

- Added newer feed/watch-page ad filtering for `sponsoredVideo`, `sponsoredDisplay`, and `promotedContents` structures.
- Added response-side handling for encrypted `googlevideo.com/initplayback` Onesie/UMP playback, not just request-side handling.
- Added current key/config handling for `config` and `log_event`.
- Added `player/ad_break` request handling.
- Added Loon rewrites for legacy `pagead`, `ptracking`, and ad-stat endpoints.
- Kept Maasea's Loon-aware player/settings path for background capability, subtitle translation, and UI options.
- Archived the previous Maasea-only plugin under `legacy/` for rollback.

## 2026-10-05 — initplayback hotfix

- Broadened the Loon initplayback request hook so current YouTube initplayback URLs are intercepted even when the older `&ack` marker is absent.
- Switched the plugin back to Loon's current `response if ... then script(...)` / `request if ... then script(...)` syntax.
- Kept YouTube-only UDP/QUIC fallback and the native-PiP compatibility response patch.

## 2026-10-05

Initial public release.

- Based on Maasea's current YouTube Enhance scripts (response build 2026-07-19; request build 2026-07-12).
- Self-hosted the current request script and a minimally patched response script for stable Loon subscriptions.
- Added YouTube-only UDP/QUIC rejection for `youtube.com`, `googlevideo.com`, and `youtubei.googleapis.com` to reduce requests bypassing MITM and causing intermittent ad leakage.
- Removed only Maasea's forced PiP capability injection while preserving background-play capability, allowing current iOS/YouTube native PiP to handle the floating window.
- Kept Maasea's current `initplayback` and `log_event` handling.
- Added selectable subtitle translation languages.
- Added a stable raw Loon subscription URL.
