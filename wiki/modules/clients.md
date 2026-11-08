---
description: Node 与 Plugin gRPC、tn-cli 和 Python chatbot 示例。
tags:
  - wiki
  - module
  - grpc
  - cli
  - chatbot
title: 协议、tn-cli 与 Python 机器人
---
# 协议、tn-cli 与 Python 机器人

仓库包含 protobuf 协议产物、命令行客户端和聊天机器人示例，用于测试或扩展客户端集成。

## 两种 gRPC 角色

[`model_grpc.pb.go`](../../pbx/model_grpc.pb.go#L21-L31) 的 `Node` 提供双向 `MessageLoop` 和文件 RPC；[`Plugin`](../../pbx/model_grpc.pb.go#L278-L297) 提供六个 unary RPC，由 Tinode 调用插件。它们是不同服务，不能互换。

## tn-cli

[`tn-cli.py`](../../tn-cli/tn-cli.py#L46-L116) 是命令入口；[`client.py`](../../tn-cli/client.py#L198-L214) 使用 `NodeStub.MessageLoop` 收发消息。hi/login 命令构造位于 [`client.py`](../../tn-cli/client.py#L92-L195)，cookie 默认保存在 `.tn-cli-cookie`，读写见 [`client.py`](../../tn-cli/client.py#L58-L81)。Cookie 是会话凭据，应妥善保护，不要提交真实 token。

## chatbot

[`chatbot.py`](../../chatbot/python/chatbot.py#L402-L419) 启动示例；[`Plugin` 服务端](../../chatbot/python/chatbot.py#L221-L230) 和 [`Node` 客户端](../../chatbot/python/chatbot.py#L232-L250) 同时存在。机器人以随机 quote 回复，断线每 3 秒重连，见 [`chatbot.py`](../../chatbot/python/chatbot.py#L262-L273) 与 [`chatbot.py`](../../chatbot/python/chatbot.py#L386-L392)。

> Account CREATE 后的主动订阅仍是 TODO，见 [`chatbot.py`](../../chatbot/python/chatbot.py#L138-L155)。不能假设机器人已自动加入每个新账号的 P2P 对话。

[[wiki/flows/connection|客户端连接]] · [[wiki/concepts/messages|消息协议]] · [[wiki/architecture/extensions|插件边界]]
