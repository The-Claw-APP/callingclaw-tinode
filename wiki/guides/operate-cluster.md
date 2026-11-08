---
description: 集群拓扑、选主、故障转移和节点变更注意事项。
tags:
  - wiki
  - guide
  - cluster
  - operations
title: 配置与运维集群
---
# 配置与运维集群

集群需要每个节点有一致拓扑、共享持久层和可达的内部 RPC 地址。

## 规划

1. 为所有节点分配唯一名称与内部监听地址。
2. 配置 `cluster_config.self` 和包含本节点的 `nodes`；命令行 `cluster_self` 可覆盖本节点名。
3. 保证数据库、文件存储与推送/通知的跨节点语义。
4. 同机测试时同时错开 HTTP、gRPC 和集群端口，示例见 [`INSTALL.md`](../../INSTALL.md#L156-L196)。

## 路由与故障转移

普通主题按一致性哈希选主，见 [[wiki/concepts/master-proxy|Master / Proxy]]。仅配置多节点不会自动启用 leader failover；`failover.enabled` 需要至少三个总节点，约束在 [`cluster_leader.go`](../../server/cluster_leader.go#L88-L124)。

`heartbeat`、`vote_after` 和 `node_fail_after` 分别控制心跳间隔、选举阈值和节点失效判定，见 [`INSTALL.md`](../../INSTALL.md#L174-L189)。不要把示例数字当作适合所有环境的推荐值。

## 发布与变更

- 拓扑变更要让各节点的环与签名一致，否则请求可能被拒绝。
- 节点重启有 fingerprint 检测；网络队列有界，客户端需处理错误和重连。
- 重哈希会清理失效代理状态，不保证客户端完全无感。
- 监控 leader、活动集群节点、主题和 RPC 相关错误，见 [[wiki/guides/observability|监控排障]]。

[[wiki/architecture/cluster|集群设计]] · [[wiki/flows/cluster-routing|请求路径]] · [[wiki/modules/cluster|代码模块]]
