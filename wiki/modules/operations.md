---
description: 数据库初始化器、expvar exporter 和 Compose 部署边界。
tags:
  - wiki
  - module
  - operations
  - monitoring
  - database
title: 数据库初始化、监控与部署
---
# 数据库初始化、监控与部署

外围工具负责部署生命周期，而不是消息业务本身。

## tinode-db

[`main`](../../tinode-db/main.go#L194-L371) 解析适配器、配置、数据文件、reset/upgrade 等参数并打开 store。只有 reset 或新建数据库才加载初始数据；已有且版本正确的数据库忽略 `--data`，分支见 [`main.go`](../../tinode-db/main.go#L315-L319)。

当前工作树在 [`main.go`](../../tinode-db/main.go#L259-L270) 对建库时 `already exists` 有重试处理；这是原有未提交改动，本 Wiki 不将它描述为上游特性。

## exporter

[`monitoring/exporter/main.go`](../../monitoring/exporter/main.go#L57-L211) 默认监听 `:6222`，支持 InfluxDB 和独立 Prometheus registry。[`scraper.go`](../../monitoring/exporter/scraper.go#L31-L112) 抓取 Go expvar JSON，处理数值和 histogram。exporter 是独立进程，不是 Tinode 内部推送器。

## 容器与部署

`docker/docker-compose/single-instance.yml` 组合 MySQL 8、Tinode 和 exporter；其中的 Influx 配置含占位值，必须结合实际部署检查。仓库还有面向本 fork 的 `docker/tinode/Dockerfile.fork` 未跟踪文件，不属于本次文档的已验证默认路径。

多节点和 ICE 的外部依赖见 [[wiki/guides/operate-cluster|集群指南]]。启动步骤见 [[wiki/guides/build-and-run|构建与运行]]，指标排障见 [[wiki/guides/observability|监控排障]]。
