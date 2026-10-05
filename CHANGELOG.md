# Changelog

## 2026-10-05

Initial public release.

- Based on current Maasea YouTube Enhance flow.
- Added YouTube-only UDP/QUIC rejection for `youtube.com`, `googlevideo.com`, and `youtubei.googleapis.com`.
- Added selectable subtitle translation languages.
- Added a patched response script that keeps background playback capability but removes forced PiP capability injection, allowing current iOS/YouTube native PiP to handle the window.
- Kept the current Maasea `initplayback` and `log_event` request handling.
- Added stable Loon raw subscription URL.
