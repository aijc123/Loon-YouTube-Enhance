# YouTube Enhance for Loon

[English](./README.md) | **简体中文**

一个面向 **Loon + iOS/iPadOS** 的 YouTube / YouTube Music 增强插件，基于 Maasea 当前上游逻辑，并针对 Loon 的 QUIC、后台播放和 PiP 兼容做额外适配。


## 当前版本（2026-10-05）

当前主版本已经不再混用两套 response 过滤器，而是改成一条统一的当前 YouTube Onesie/UMP 处理链路。参考实现于 2026-10-04 刚更新了新版 Premium banner / Sponsored 结构识别，并直接处理加密 `initplayback` response。

本仓库将对应核心脚本固定在 `scripts/core/`，并额外做 Loon 参数适配、字幕翻译以及 YouTube 专用 QUIC 回退。旧版保存在 `legacy/`，可随时回滚。


## 当前广告处理架构

当前版本不再把希望主要寄托在 `pagead` tracking 或单一 `oad` URL 规则上。真正的主链路是：

1. 在 `/youtubei/v1/player` 和 `get_watch` **请求阶段**删除广告 signals / ad params / force-ad params，并写入 inline-no-ad 标志；
2. 精确处理 `/youtubei/v1/player/ad_break`，返回合法空 Protobuf，阻止新的片头/中插广告配置；
3. 对 iOS YouTube App 的 POST `googlevideo.com/initplayback` 使用 Loon 原生 `reject_video(200)`，让客户端回退到已经净化的 Player 链路；
4. `browse/next/search` 独立清理 Sponsored 信息流卡片；
5. Maasea/Kelee response 路径继续负责后台播放、字幕翻译和界面开关。

播放器请求与信息流实现参考并固定于 MIT 许可的 [teaoea/shell](https://github.com/teaoea/shell) 提交 `a44ce03f12368d44f4a324eb2c0c18c00d60e937`，发布副本保存在本仓库 `vendor/teaoea/`，不会随上游静默改变。

## 功能

- 额外过滤新版播放页/推荐流中的 Sponsored 广告卡片
- 直接处理加密 `initplayback` Onesie/UMP 响应，改善后台播放和广告过滤稳定性
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


## 当前兼容链路说明

当前版本继续保留 Maasea 的 Loon-aware 播放/设置逻辑，同时参考并直接调用公开的 `gholts/surge` YouTube 兼容脚本来处理新版 Sponsored 卡片、加密 Onesie/UMP 响应以及 `player/ad_break`。其模块说明该实现基于 Maasea 的 Apache-2.0 代码。

