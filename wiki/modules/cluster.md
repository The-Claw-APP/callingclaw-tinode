---
description: 集群节点、主题选主、跨节点代理与故障转移实现。
tags:
  - wiki
  - module
  - cluster
  - ringhash
title: 集群与一致性哈希
---
# 集群与一致性哈希

本模块包含节点拓扑、主题归属、跨节点 RPC 代理和可选 leader 故障转移。所有在线主题状态仍由各节点的 Hub/Topic 持有。

## 组件

- [`clusterInit`](../../server/cluster.go#L944-L1036)：读取拓扑、选择本节点、构造 Ring 与 UID worker ID。
- [`Cluster.start`](../../server/cluster.go#L1046-L1084)：注册并监听 `net/rpc`，启动异步调用和发送循环。
- [`runProxy`](../../server/topic_proxy.go#L17-L117)：将本地客户端事件转换为 Master 请求。
- [`Cluster.TopicMaster`](../../server/cluster.go#L469-L613)：Master 侧接入代理会话并处理请求。
- [`Ring`](../../server/ringhash/ringhash.go#L69-L128)：20 个虚拟节点、二分选主、环签名。
- [`clusterFailoverConfig`](../../server/cluster_leader.go#L43-L52)：心跳、投票、线程和超时配置。

## 核心约束

每条跨节点请求携带环签名，Master 拒绝不再匹配当前归属的请求。节点连接有重连、fingerprint 和异步写队列，但这些不是持久消息队列。

failover 是可选能力，总节点数至少为 3；Leader 故障时 follower 争取多数票。不要仅凭“多节点”就假设已经开启 leader failover，也不要把它与客户端 gRPC 混淆。

[[wiki/architecture/cluster|集群架构]] · [[wiki/flows/cluster-routing|跨节点流程]] · [[wiki/concepts/master-proxy|角色定义]] · [[wiki/guides/operate-cluster|部署指南]]
