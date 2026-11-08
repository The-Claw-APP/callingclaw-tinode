---
description: 呼叫邀请、接受、SDP/ICE 信令转发和结束状态。
tags:
  - wiki
  - flow
  - webrtc
  - call
title: WebRTC 呼叫建立与结束
---
# WebRTC 呼叫建立与结束

呼叫通过 P2P 主题中的 `{pub}` 邀请和 `{note what=call}` 事件进行。SDP/ICE payload 由服务端转发；实际媒体连接在客户端与 ICE 基础设施之间建立。

```mermaid
sequenceDiagram
  participant A as 主叫客户端
  participant T as P2P Topic
  participant B as 被叫客户端
  A->>T: {pub} 带 webrtc 头，创建邀请
  T->>T: 保存消息，currentCall = 1 party，启动超时
  T-->>B: {data} 呼叫邀请
  B->>T: {note call ringing}
  T-->>A: {info call ringing}
  B->>T: {note call accept}
  T->>T: 保存 accepted 替代消息，加入第二个 party
  T-->>A: {info call accept}
  A->>T: {note offer / ice-candidate}
  T-->>B: {info} 转发 payload
  B->>T: {note answer / ice-candidate}
  T-->>A: {info} 转发 payload
  A->>T: {note hang-up}
  T->>T: 保存 finished、广播 hang-up、清空 currentCall
```

[`handleCallInvite`](../../server/calls.go#L222-L237) 记录邀请的 seq 和内容。[`handleCallEvent`](../../server/calls.go#L241-L373) 验证 seq 和参与者身份：accept 只允许尚未建立时的被叫；offer/answer/ICE 只允许已建立的两个 party。

接受后停止建立计时器。结束时 [`maybeEndCallInProgress`](../../server/calls.go#L377-L430) 区分 finished、missed、declined、disconnected，并用 [`messageHead`](../../server/calls.go#L174-L193) 写入替代关系、状态和可选时长。

[[wiki/modules/calls|呼叫模块]] · [[wiki/concepts/messages|info 与 data]] · [[wiki/flows/publish|消息保存]]
