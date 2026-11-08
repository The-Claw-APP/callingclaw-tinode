---
description: 按源码构建服务、初始化数据库并检查运行配置。
tags:
  - wiki
  - guide
  - build
  - run
  - database
title: 构建、数据库初始化与运行
---
# 构建、数据库初始化与运行

本指南对应仓库的源码布局；命令应在仓库根目录执行。数据库必须已启动，配置中的凭据与存储位置需按环境修改。

## 构建

安装说明见 [`INSTALL.md`](../../INSTALL.md#L29-L78)。编译服务端和初始化器时必须选择数据库 build tag：

```bash
go build -tags postgres ./server
go build -tags postgres ./tinode-db
```

也可以将 `postgres` 换成 `mysql`、`mongodb`，或按 [`INSTALL.md`](../../INSTALL.md#L41-L70) 使用多个 tag。不要用未选择适配器的构建结果推断数据库可用性。

## 初始化数据库

先检查 [`server/tinode.conf`](../../server/tinode.conf#L1-L60) 和初始化器配置，再运行：

```bash
go run -tags postgres ./tinode-db -config=./tinode-db/tinode.conf
```

首次安装可选 `-data=./tinode-db/data.json` 加载样例。已有正确版本数据库不会重复加载样例；升级和 reset 是不同操作，先阅读 [`tinode-db` 入口](../../tinode-db/main.go#L194-L371)，不要对生产数据轻率使用 reset。

## 启动

```bash
go run -tags postgres ./server -config=./server/tinode.conf
```

示例监听 WebSocket/HTTP `:6060`、gRPC `:16060`。静态客户端目录通过 `-static_data` 指定；模板和静态目录的路径要按本机布局配置，见 [`INSTALL.md`](../../INSTALL.md#L100-L151)。

## 上线前检查

- 数据库适配器和 DSN 匹配，初始化/迁移已完成。
- API key salt、TLS、消息大小和文件大小限制按环境设置。
- 媒体 fs 的目录在集群中必须共享，或改用适当外部存储。
- 推送、邮箱/电话验证和 ICE 是外部依赖，样例值不是生产配置。
- 不要提交 `.tn-cli-cookie`、数据库密码或真实 API/ICE 凭据。

[[wiki/modules/operations|运维模块]] · [[wiki/guides/operate-cluster|集群运行]] · [[wiki/guides/observability|监控]]
