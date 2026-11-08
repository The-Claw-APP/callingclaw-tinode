---
description: 服务进程、配置、WebSocket、gRPC 和统一消息分发。
tags:
  - wiki
  - module
  - server
  - network
title: Go 服务端入口与网络
---
# Go 服务端入口与网络

本模块负责配置装配、传输适配和进程级生命周期。业务处理不分别复制到 WebSocket、gRPC 和 long polling 中。

- **入口与配置**：[`main`](../../server/main.go#L334-L423) 加载配置并打开数据库；[`configType`](../../server/main.go#L255-L332) 是主要配置结构。初始化顺序见 [[wiki/architecture/system|系统架构]]。
- **WebSocket**：[`serveWebSocket`](../../server/hdl_websock.go#L168-L211) 验证 API key、升级连接、创建 Session，再启动读循环和写循环。
- **gRPC**：[`grpcServer.Serve`](../../server/hdl_grpc.go#L147-L190) 实现 Node 双向消息流；`MessageLoop` 接收 protobuf，转换后交给同一 Session 分发器。
- **统一分发**：[`Session.dispatch`](../../server/session.go#L465-L619) 处理身份、请求类型与插件，并路由到 `hi/login/acc/sub/pub/note/get/set`。
- **运行时**：SessionStore 管理连接身份；Hub 管理活动主题。参见 [[wiki/modules/topics|Hub 与主题]]。
- **HTTP 资源**：大文件的上传/下载与消息通道分开，路由注册见 [`main`](../../server/main.go#L777-L794)。

## 不变量与扩展入口

请求从 transport 解码后进入 `ClientComMessage`，响应经 `ServerComMessage` 返回。接入新传输时，应复用 Session 语义、身份检查与主题路由，而不是直接操作 Topic 内部字段。配置键以代码为准，样例见 [`server/tinode.conf`](../../server/tinode.conf#L1-L43)。

[[wiki/flows/connection|连接流程]] · [[wiki/concepts/messages|消息信封]] · [[wiki/OVERVIEW|总览]]
