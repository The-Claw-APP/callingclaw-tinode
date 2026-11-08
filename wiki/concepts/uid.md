---
description: UID、用户 ID、P2P 和特殊主题的路由名称。
tags:
  - wiki
  - concept
  - uid
  - topics
title: UID 与主题命名
---
# UID 与主题命名

UID 是数据库对象的 64 位标识；主题名是路由语义。二者相关但不能混用。

## UID 表达

[`Uid`](../../server/store/types/types.go#L57-L75) 底层为 `uint64`，0 是未初始化的 `ZeroUid`。[`UserId`](../../server/store/types/types.go#L177-L207) 生成 `usr...` 外部名称；文本编码使用 URL-safe、无 padding 的 base64。

## 主题类别与别名

| 外部名称 | 路由含义 |
| --- | --- |
| `me` | 当前用户的个人主题 |
| `fnd` / `slf` | 当前用户的查找/自有主题 |
| `usr...` | 当前用户与此用户的 P2P 对话 |
| `grp...` | 群组主题 |
| `chn...` | 频道形式，内部映射到群组名称 |
| `sys` | 系统主题 |

[`TopicCat`](../../server/store/types/types.go#L1355-L1367) 在运行时表示类别；[`expandTopicName`](../../server/session.go#L1332-L1367) 将客户端名称展开为全局路由键。

P2P 内部名由双方 UID 排序后拼接，[`P2PName`](../../server/store/types/types.go#L294-L339) 保证双方得到同一个 `p2p...` 名称，并禁止自己与自己建 P2P。[`P2PNameForUser`](../../server/store/types/types.go#L342-L353) 在返回客户端时转换为对端 `usr...`。

修改名称转换时同时检查路由、存储、权限和每个接收者的显示名，不能只修改客户端字符串。

[[wiki/flows/subscription|订阅与名称展开]] · [[wiki/architecture/topics|主题运行时]] · [[wiki/concepts/master-proxy|路由角色]]
