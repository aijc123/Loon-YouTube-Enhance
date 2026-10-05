# YouTube Enhance for Loon

[English](./README.md) | **简体中文**

一个面向 **Loon + iOS/iPadOS** 的 YouTube / YouTube Music 增强插件，基于 Maasea 当前上游逻辑，并针对 Loon 的 QUIC、后台播放和 PiP 兼容做额外适配。

## 功能

- YouTube / YouTube Music 去广告
- 后台播放
- 使用 iOS / YouTube 原生 PiP，减少脚本强制 PiP 导致的黑屏/暂停
- 字幕翻译语言下拉选择
- 隐藏上传按钮 / Shorts / YouTube Music 选段按钮
- 仅对 YouTube 相关域名禁用 UDP/QUIC，自动回退 TCP/HTTPS
- 固定 Raw URL，可在 Loon 中直接刷新更新

## Loon 订阅

```
https://raw.githubusercontent.com/aijc123/Loon-YouTube-Enhance/main/YouTube_Enhance.lpx
```

## 安装

1. 在 Loon 中停用其它 YouTube 去广告 / Enhance 插件。
2. 删除以前手工添加的 YouTube QUIC Local Rule，避免重复规则。
3. 添加上面的 Raw URL 作为插件订阅。
4. 确认 MitM 已开启且证书已信任。
5. 更新插件后彻底退出 YouTube，再重新打开一次。

## 为什么做这个 fork

新版 YouTube/iOS 的播放链路和 PiP 行为持续变化。这个项目在 Maasea 当前脚本的基础上做两类 Loon 专用适配：

### 1. YouTube QUIC 回退

只对以下域名拒绝 UDP/QUIC，让它们回退到 TCP/HTTPS：

- `youtube.com`
- `googlevideo.com`
- `youtubei.googleapis.com`

不会全局关闭 UDP/443。

### 2. 原生 PiP 兼容

Maasea 上游 response 脚本会同时注入 PiP 和后台播放 capability。这个 fork 只移除强制 PiP capability 注入，保留后台播放，让当前 iOS / YouTube 自己处理 PiP。

### 3. initplayback 热修

部分当前 YouTube 请求不再带旧版规则依赖的 `&ack` 标记。插件现在匹配所有 `googlevideo.com/initplayback` 请求，避免这部分播放/广告链路绕过 request script。

## 排障

如果又出现广告或无法播放，不要先重启 YouTube。先打开 Loon → Requests，搜索：

- `player`
- `next`
- `initplayback`
- `log_event`
- `googlevideo`

正常情况下，`initplayback` 和需要处理的 youtubei 请求应出现 Script 标记。

## 上游

核心解析逻辑来自 [Maasea/sgmodule](https://github.com/Maasea/sgmodule)。本仓库是非官方 Loon 兼容 fork，与 YouTube、Google、Loon 或 Maasea 无隶属关系。
