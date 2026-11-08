---
title: Wiki Log
description: Append-only audit trail of wiki generation and refresh runs.
---

# Wiki Log

Append-only audit trail. Add one dated entry per generation or refresh run, recording the profile, the `source_commit` it was anchored to, and the coverage. The codebase-wiki skill describes the entry shape.

## 2026-11-08 — 初次生成完整源码 Wiki

- **Profile**：`internal/standard`
- **Source commit**：`22a7c18e9cd695e9a061bf1b8c84175196ef5a15`
- **范围**：Go 服务端的会话、主题、消息、存储、身份、集群、WebRTC 信令；Python CLI/机器人、gRPC、数据库初始化、监控和部署。
- **排除**：第三方依赖、生成代码、测试数据、静态资源及未展开的其他客户端。
- **覆盖**：[[wiki/OVERVIEW|总览]]；4 个架构页；8 个模块页；6 个流程页；5 个概念页；4 个操作指南。
- **验证**：对 `wiki` 下 29 个文档执行 OpenKnowledge 审计，未发现坏链；总览链接全部 28 个内容页，图检查未发现无入链的内容页。
- **工作树说明**：保留 `tinode-db` 的原有未提交修改，未将它当作上游功能；未修改源码。
