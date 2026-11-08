---
description: 主题主节点和代理节点的状态、写入与会话边界。
tags:
  - wiki
  - concept
  - master
  - proxy
title: Master 与 Proxy Topic
---
# Master 与 Proxy Topic

同一个逻辑主题在集群中由 Master 承载业务状态，其他节点上的 Proxy 维护本地客户端连接并转发请求。

| 属性 | Master | Proxy |
| --- | --- | --- |
| 选举 | 一致性哈希按主题名选主 | 不是选出的另一个主副本 |
| 持久化 | 调用 Store 保存消息和主题状态 | 不直接执行业务写入 |
| 会话 | 通过 multiplex/proxy session 表示远端客户端 | 持有本地普通客户端 Session |
| 循环 | `runLocal` | `runProxy` |
| 返回 | `ClusterResp` 广播或定向回包 | 解包后向本地客户端发送 |

[`Topic.run`](../../server/topic.go#L218-L224) 选择循环，Proxy 转发见 [`runProxy`](../../server/topic_proxy.go#L17-L117)。代理不是一份完整主题数据库副本，也不是第二个同时独立分配 seq 的写入者。

请求带环签名，防止拓扑改变后旧 Master 继续处理。环变化可能使订阅失效并要求重建，见 [[wiki/flows/cluster-routing|转发流程]]。

[[wiki/architecture/cluster|集群拓扑]] · [[wiki/modules/cluster|集群实现]] · [[wiki/concepts/subscriptions|会话与订阅]]
