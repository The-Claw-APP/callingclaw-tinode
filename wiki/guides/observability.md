---
description: expvar、exporter、Prometheus/InfluxDB 和分层排障清单。
tags:
  - wiki
  - guide
  - monitoring
  - troubleshooting
title: 监控与排障
---
# 监控与排障

服务通过 Go expvar 暴露运行状态，`monitoring/exporter` 将其转换为 InfluxDB 或 Prometheus 输出。

## 指标路径

[`server/tinode.conf`](../../server/tinode.conf#L58-L66) 的 `expvar` 示例为 `/debug/vars`；exporter 的默认抓取地址却是 `http://localhost:6060/stats/expvar`，见 [`main.go`](../../monitoring/exporter/main.go#L61-L69)。**部署时必须核对实际路径，不要直接假定两个示例默认值互相匹配。**

exporter 默认 `:6222`，`serve_for` 可选 `influxdb` 或 `prometheus`。[`main.go`](../../monitoring/exporter/main.go#L97-L127) 校验服务类型及 Influx 参数；Prometheus 使用独立 registry 暴露 `/metrics`，见 [`main.go`](../../monitoring/exporter/main.go#L148-L170)。

默认数值指标包括 `LiveTopics`、`TotalTopics`、`LiveSessions`、`ClusterLeader`、`TotalClusterNodes`、`LiveClusterNodes`，histogram 包括 `RequestLatency` 和 `OutgoingMessageSize`。

## 按层排查

| 症状 | 优先检查 |
| --- | --- |
| 无法连接或登录 | API key、传输端口、hi/login 状态、认证配置 |
| 主题订阅失败 | 名称展开、权限、初始化失败日志、数据库可用性 |
| 消息未广播 | `Messages.Save` 错误、有效写权限、Session 出站队列 |
| 跨节点失败 | Ring 签名、Master 归属、内部 RPC、fingerprint、队列过载 |
| 呼叫无法建立 | 是否启用、ICE 配置、P2P 约束、呼叫 seq/参与者 |
| 上传失败 | 文件大小、认证、媒体 Handler、文件元数据与字节存储 |

`/debug/status` 是调试端点，配置注释明确它不是健康检查，见 [`tinode.conf`](../../server/tinode.conf#L62-L66)。生产暴露 expvar/debug 前应做访问控制，避免公开内部运行信息。

[[wiki/modules/operations|exporter 模块]] · [[wiki/guides/build-and-run|启动检查]] · [[wiki/guides/operate-cluster|集群运维]]
