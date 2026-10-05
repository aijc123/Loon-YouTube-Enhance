# Changelog

## 2026-10-05

Initial public release.

- Based on Maasea's current YouTube Enhance scripts (response build 2026-07-19; request build 2026-07-12).
- Self-hosted the current request script and a minimally patched response script for stable Loon subscriptions.
- Added YouTube-only UDP/QUIC rejection for `youtube.com`, `googlevideo.com`, and `youtubei.googleapis.com` to reduce requests bypassing MITM and causing intermittent ad leakage.
- Removed only Maasea's forced PiP capability injection while preserving background-play capability, allowing current iOS/YouTube native PiP to handle the floating window.
- Kept Maasea's current `initplayback` and `log_event` handling.
- Added selectable subtitle translation languages.
- Added a stable raw Loon subscription URL.
