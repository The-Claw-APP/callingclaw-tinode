---
description: 存储外观、数据库适配器、消息保存和共享领域类型。
tags:
  - wiki
  - module
  - storage
  - database
title: 持久化与数据类型
---
# 持久化与数据类型

`server/store` 将数据类型、业务外观和数据库适配器分层。不要把某个适配器的实现细节当成所有后端共同保证。

## 分层

- **领域类型**：`server/store/types` 定义 `Uid`、用户、主题、订阅、消息和 `AccessMode`。
- **适配器契约**：[`Adapter`](../../server/db/adapter.go#L14-L209) 定义对象 CRUD、认证记录、消息、设备、文件、缓存与数据库生命周期。
- **统一外观**：[`Store`](../../server/store/store.go#L109-L146) 管理适配器；`Users`、`Topics`、`Subs`、`Messages` 等 mapper 在适配器之上补充领域逻辑。
- **扩展子系统**：认证、凭据验证、媒体也通过 store 接口暴露，见 [[wiki/architecture/extensions|扩展架构]]。

## 消息保存不是单次裸插入

[`messagesMapper.Save`](../../server/store/store.go#L682-L727) 先设置 UID，再更新主题消息序号，保存消息，按条件更新发送者的已读游标，最后链接附件文件 ID。调用链与广播先后关系见 [[wiki/flows/publish|发布流程]]。

## UID 和权限

`Uid` 是 `uint64`，文本形式使用 URL-safe base64；`UserId` 添加 `usr` 前缀，见 [`Uid.UserId`](../../server/store/types/types.go#L177-L207)。权限是位掩码而非角色字符串，见 [[wiki/concepts/subscriptions|订阅与权限]]。

## 修改存储代码时

先确认操作属于哪个 mapper 和 adapter 方法；接口变化要覆盖所有启用的后端及测试。`tinode-db` 负责建库和升级，见 [[wiki/guides/build-and-run|初始化指南]]。新后端接入见 [[wiki/guides/extend-backend|扩展后端]]。

[[wiki/concepts/uid|UID 与命名]] · [[wiki/concepts/messages|消息模型]] · [[wiki/modules/identity|身份模块]]
