# Loon YouTube Enhance

**English** | [简体中文](./README.zh-CN.md)

A Loon-focused YouTube / YouTube Music enhancement plugin based on Maasea's current player/settings logic plus a newer encrypted Onesie/UMP compatibility path for current YouTube builds.

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


## Additional compatibility reference

The current feed/Onesie compatibility path references the actively maintained public `gholts/surge` YouTube scripts for newer Sponsored-card structures and encrypted UMP handling. That project describes its YouTube implementation as based on Maasea's Apache-2.0 code.

