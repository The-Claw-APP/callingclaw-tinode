---
description: 持久订阅、在线附着、want/given 权限位与有效权限。
tags:
  - wiki
  - concept
  - subscription
  - acl
title: 订阅与访问权限
---
# 订阅与访问权限

一个用户拥有持久订阅，一个在线 Session 附着到主题；这是两层状态。权限由 `want`（希望拥有）和 `given`（实际授予）共同决定。

## 两层订阅

- `types.Subscription` 表示持久关系及读/收游标、权限等。
- `Session.subs` 中的 `Subscription` 表示到某个活动 Topic 的 channel 绑定，见 [`server/session.go`](../../server/session.go#L163-L177)。
- `Topic.perUser` 缓存用户权限和读数；`Topic.sessions` 表示当前附着连接。

普通 leave 不应默认解释为删除持久订阅；附着成功才建立相应的内存路由，见 [[wiki/flows/subscription|订阅流程]]。

## 权限位

[`AccessMode`](../../server/store/types/types.go#L533-L570) 的常用位包括：

| 位 | 含义 |
| --- | --- |
| J | 加入主题 |
| R | 接收广播 |
| W | 发布内容 |
| P | 接收 presence |
| A / S | 审批 / 邀请 |
| D | 硬删除消息 |
| O | 所有者 |

`N` 表示无权限，不能与其他位组合，解析见 [`ParseAcs`](../../server/store/types/types.go#L605-L640)。

## 有效权限

发布使用 `(modeWant & modeGiven).IsWriter()`，见 [`saveAndBroadcastMessage`](../../server/topic.go#L999-L1007)。读取和 presence 也按各自有效位判断；因此仅被授予某个权限，不一定意味着订阅者请求并接受它。

默认权限会按主题类别、是否认证、是否频道选择，见 [`getDefaultAccess`](../../server/utils.go#L243-L266)。不要以单个默认常量推断所有主题的权限。

[[wiki/flows/publish|发布权限检查]] · [[wiki/modules/storage|持久订阅]] · [[wiki/concepts/master-proxy|代理权限]]
