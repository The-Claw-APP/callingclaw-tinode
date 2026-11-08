---
description: 认证处理器、凭据验证器、REST 示例与插件边界。
tags:
  - wiki
  - module
  - auth
  - credentials
  - plugins
title: 身份、凭据验证与外部集成
---
# 身份、凭据验证与外部集成

登录认证和邮箱/电话验证是两条不同的扩展路径，最终都影响用户身份、权限或标签。

## 认证

[`AuthHandler`](../../server/auth/auth.go#L231-L280) 定义初始化、认证记录增改删、认证、唯一性检查、token 生成和受限标签。`basic` 处理器解析 `username:password`，检查策略并用 bcrypt 保存，见 [`auth_basic.go`](../../server/auth/basic/auth_basic.go#L56-L151)。

[`GetLogicalAuthHandler`](../../server/store/store.go#L855-L908) 先解析逻辑名映射，再取实际处理器。因此配置和消息中的 scheme 不一定等于 Go 实现包名。

## 凭据与外部服务

- 启动按认证等级注册 validator；账号创建先 pre-check，再保存或发送验证，见 [[wiki/flows/account|账号流程]]。
- [`rest-auth/auth.py`](../../rest-auth/auth.py#L27-L62) 是 `/auth` 示例，[`/link`](../../rest-auth/auth.py#L76-L101) 是账号链接示例；它使用 dummy 数据，不是生产用户数据库。
- 插件通过独立的 gRPC `Plugin` 服务接收事件或参与过滤；`Node` gRPC 服务方向相反，供客户端调用。
- 媒体和推送由独立接口接入；消息保存时可以关联附件，发布后可构造推送，见 [[wiki/modules/topics|主题模块]]。

## 修改提示

新增认证机制要实现完整 `AuthHandler` 契约，处理过期、标签、唯一性和记录清理。外部 REST 示例不可直接作为安全部署依据。

[[wiki/architecture/extensions|扩展架构]] · [[wiki/modules/clients|gRPC 与机器人]] · [[wiki/guides/extend-backend|扩展指南]]
