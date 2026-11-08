---
description: 通过既有接口增加数据库、认证、验证、媒体和推送实现。
tags:
  - wiki
  - guide
  - extension
  - interfaces
title: 扩展存储、认证与媒体后端
---
# 扩展存储、认证与媒体后端

优先实现既有接口并注册，不要让业务层直接依赖新后端的具体类型。

## 选择扩展点

| 目标 | 接口 | 注册/选择 |
| --- | --- | --- |
| 数据库 | [`adapter.Adapter`](../../server/db/adapter.go#L14-L209) | 包 init 调 `store.RegisterAdapter`，配置 `use_adapter` |
| 登录认证 | [`auth.AuthHandler`](../../server/auth/auth.go#L231-L280) | `RegisterAuthScheme`，可用逻辑别名映射 |
| 邮箱/电话等验证 | `validate.Validator` | `store.RegisterValidator` |
| 文件字节存储 | `media.Handler` | `store.RegisterMediaHandler` + `UseMediaHandler` |
| 推送 | `push.Handler` | `push.Register`，可多个 ready handler |

各注册表集中在 [`server/store/store.go`](../../server/store/store.go#L798-L1005) 及 push 包。不要混淆“注册实现”和“在配置中选择实现”。

## 数据库实现

1. 实现 Adapter 的全部生命周期、用户、认证、主题、订阅、搜索、消息、设备、文件和缓存方法。
2. 保留 UID、时间、权限位和软/硬删除语义。
3. 通过 init 注册唯一且稳定的名称，并使用对应 build tag 引入。
4. 添加后端测试，覆盖消息保存、游标、认证唯一键和文件关联。
5. 确认 `tinode-db` 的建库/升级路径与服务运行兼容。

## 认证或凭据实现

认证器处理 secret、challenge、过期、标签、记录生命周期，见 [[wiki/modules/identity|身份模块]]。验证器处理 pre-check、发送、检查和删除凭据。二者都可通过 `store.Users` 持久化，避免绕过统一错误语义。

## 媒体与推送

媒体字节和数据库文件记录是配套关系：上传/下载/删除实现仍需维护元数据一致性。推送 handler 的输入队列是非阻塞 fan-out，阻塞可能造成该次投递被丢弃；设备 token 持久化仍由 store 负责。

[[wiki/architecture/extensions|扩展架构]] · [[wiki/modules/storage|数据库分层]] · [[wiki/guides/build-and-run|构建验证]]
