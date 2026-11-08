---
description: 客户端和服务端消息包装、控制回执、data、info、pres。
tags:
  - wiki
  - concept
  - protocol
  - messages
title: 消息信封与主题协议
---
# 消息信封与主题协议

客户端和服务端使用明确的消息类型区分命令、控制回执、内容、元数据和状态通知。

## 客户端到服务端

[`ClientComMessage`](../../server/datamodel.go#L335-L371) 是 `{hi}`、`{acc}`、`{login}`、`{sub}`、`{leave}`、`{pub}`、`{get}`、`{set}`、`{del}`、`{note}` 的外层包装。

[`MsgClientPub`](../../server/datamodel.go#L265-L272) 包含 topic、noecho、head 和 content；[`MsgClientSub`](../../server/datamodel.go#L169-L188) 可同时携带 set/get 查询。

## 服务端到客户端

[`ServerComMessage`](../../server/datamodel.go#L830-L856) 包含：

- `ctrl`：请求的接受、错误或状态回执；[`MsgServerCtrl`](../../server/datamodel.go#L583-L592) 携带 HTTP 风格 code、id、topic 和 params。
- `data`：主题内容；[`MsgServerData`](../../server/datamodel.go#L607-L617) 含 seq、from、head、content。
- `meta`：查询结果。
- `pres`：权威 presence/权限状态。
- `info`：读回执、输入状态及呼叫信令等。

典型映射是 `{pub} → {ctrl} + {data}` 和 `{note} → {info}`。信封中部分字段仅在集群内部使用，不会按 JSON 发给客户端。

JSON 与 protobuf 只是编码差异，转换后复用同一数据模型。内容格式见 [[wiki/concepts/drafty|Drafty]]，完整保存与广播见 [[wiki/flows/publish|发布流程]]。

[[wiki/flows/connection|hi/login]] · [[wiki/flows/video-call|info 呼叫信令]] · [[wiki/modules/clients|gRPC 客户端]]
