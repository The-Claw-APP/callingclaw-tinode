---
description: 主题名展开、惰性加载/创建、持久订阅和在线附着。
tags:
  - wiki
  - flow
  - subscription
  - topic
title: 创建或订阅主题
---
# 创建或订阅主题

客户端看到的 `me`、P2P 对端用户名和频道名，会先展开为全局可路由名称，再进入 Hub。

```mermaid
sequenceDiagram
  participant C as Session
  participant H as Hub
  participant T as Topic
  participant DB as Store
  C->>C: expandTopicName
  C->>H: join {sub}
  H->>H: 查 topics；没有则创建 paused Topic
  H->>T: topicInit
  T->>DB: 加载已有主题或按类别创建
  DB-->>T: 主题、订阅与权限数据
  T->>T: 解除 paused，运行 local/proxy
  C->>T: join / subscriptionReply
  T->>DB: 必要时创建持久订阅
  T-->>C: {ctrl}，附着 Session channels
```

[`expandTopicName`](../../server/session.go#L1332-L1367) 将 `me/fnd/slf` 映射到当前用户，`usr...` 映射为双方确定的 P2P 名，`chn...` 转成内部群组名。自己与自己创建 P2P 会被拒绝。

[`Hub.run`](../../server/hub.go#L151-L220) 异步调用 [`topicInit`](../../server/init_topic.go#L21-L135)，以免存储加载阻塞 Hub。`topicInit` 按类别处理 me、fnd、P2P、grp/chn、sys 和 slf；失败时清理 Hub 中未完成的主题。

[`subscriptionReply`](../../server/topic.go#L1376-L1483) 负责创建/更新持久订阅并添加内存 Session。持久订阅和在线附着是两层：普通 `{leave}` 通常只断开连接，不等于 `Unsub` 删除订阅。

[[wiki/architecture/topics|Topic 生命周期]] · [[wiki/concepts/uid|名称展开]] · [[wiki/concepts/subscriptions|订阅与权限]]
