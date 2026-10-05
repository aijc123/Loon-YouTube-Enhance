# Promotion Copy

## 中文短版

Loon 用户如果最近遇到 YouTube 偶尔漏广告、后台播放/PiP 不稳定，可以试试这个兼容版：

**YouTube Enhance for Loon**
- 基于 Maasea 当前上游
- YouTube / YouTube Music 去广告
- 后台播放
- 原生 PiP 兼容
- 字幕翻译
- YouTube 专用 QUIC/UDP 回退
- 针对新版 `initplayback` 做匹配热修
- 固定 Raw URL，Loon 内可直接刷新更新

项目：
https://github.com/aijc123/Loon-YouTube-Enhance

订阅：
https://raw.githubusercontent.com/aijc123/Loon-YouTube-Enhance/main/YouTube_Enhance.lpx

## 中文详细版

最近新版 YouTube/iOS 的播放链路变化比较频繁，常见问题包括：偶尔漏视频广告、PiP 黑屏/暂停、后台播放状态不稳定，以及 QUIC/HTTP3 绕过脚本链路。

我做了一个 Loon 专用兼容 fork，核心解析仍跟 Maasea 上游，但针对 Loon/iOS 做了几处调整：

- 对 YouTube 相关域名单独禁用 UDP/QUIC，让它们回落 TCP/HTTPS，不影响其它 App
- 保留后台播放 capability
- 不再强制注入 PiP capability，交给当前 iOS/YouTube 原生 PiP
- 扩大新版 `googlevideo.com/initplayback` 匹配，避免旧的 `&ack` 条件造成漏处理
- 字幕翻译改成可选语言
- 使用固定 GitHub Raw 地址，后续直接在 Loon 里刷新

欢迎测试，有漏广告/播放异常时最好直接带 Loon Requests 截图提 issue。

## English

A Loon-focused YouTube / YouTube Music enhancement fork based on Maasea upstream.

Highlights:
- ad filtering
- background playback
- native iOS/YouTube PiP compatibility
- subtitle translation
- YouTube-only QUIC/UDP fallback
- current `initplayback` matching hotfix
- stable raw subscription URL for in-app updates

Repo:
https://github.com/aijc123/Loon-YouTube-Enhance
