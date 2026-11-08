---
description: P2P WebRTC 邀请、信令、呼叫状态和 ICE 配置。
tags:
  - wiki
  - module
  - webrtc
  - calls
title: WebRTC 呼叫信令
---
# WebRTC 呼叫信令

Tinode 负责在 P2P 主题中保存和转发呼叫信令；实际媒体协商依赖客户端与配置的 ICE 服务。本模块不实现 SFU 或媒体转发。

## 状态与配置

[`videoCall`](../../server/calls.go#L79-L91) 保存参与者、呼叫消息 seq、内容、MIME 和接受时间。参与者按 session ID 存储，以支持同一用户的多设备。

[`initVideoCalls`](../../server/calls.go#L116-L171) 只有启用后才读取内联 ICE 或 ICE 文件；没有有效 ICE 配置则初始化失败。呼叫建立超时未配置时使用默认值。

## 邀请与信令

[`handlePubBroadcast`](../../server/topic.go#L1092-L1134) 识别带 `webrtc` 头的发布：必须已启用 ICE、必须是 P2P 主题、不能已有呼叫；先保存消息，再 [`handleCallInvite`](../../server/calls.go#L220-L237) 设置 `currentCall` 和建立计时器。

服务端 `{info what=call}` 的事件包括 `ringing`、`accept`、`offer`、`answer`、`ice-candidate`、`hang-up`，常量见 [`calls.go`](../../server/calls.go#L24-L47)。这些事件属于主题信令，不是 RTP/媒体数据。

集群下 Master 保存呼叫，proxy 会话用仅含必要字段的 Session 副本参与，见 [`callPartySession`](../../server/calls.go#L93-L113)。

[[wiki/flows/video-call|呼叫流程]] · [[wiki/modules/topics|P2P 主题]] · [[wiki/guides/build-and-run|运行配置]]
