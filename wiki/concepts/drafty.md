---
description: Drafty 文本、格式/实体树、grapheme 与推送预览。
tags:
  - wiki
  - concept
  - drafty
  - content
title: Drafty 富文本内容
---
# Drafty 富文本内容

Drafty 将纯文本、格式范围和实体数据组合为可传输内容，常用于消息正文和通知预览。

## 数据形状

内部文档包含 `txt`、`fmt`、`ent`，定义见 [`server/drafty/drafty.go`](../../server/drafty/drafty.go#L32-L42)。文本位置按 grapheme 处理，而不是简单把 UTF-8 字节当可见字符。

[`toTree`](../../server/drafty/drafty.go#L170-L233) 把格式和实体范围转换成树：校验边界和实体索引，按起点与长度排序，过滤非法重叠范围。不要把任意带 `fmt` 的对象都当作已验证 Drafty。

## 预览

[`Preview`](../../server/drafty/drafty.go#L64-L101) 以 grapheme 数量截断，移除引用文本、前导换行和大型实体内容，返回 JSON 编码的 Drafty 摘要，适合单行推送通知。

普通字符串也可转换；对象至少要有可识别的 Drafty 字段。`PlainText` 已标记 deprecated，新开发优先使用 `Preview`。

Drafty 只描述内容，不决定写权限、存储序号或接收者；这些由 [[wiki/concepts/messages|消息信封]] 和 [[wiki/flows/publish|发布流程]] 负责。

[[wiki/modules/topics|消息模块]] · [[wiki/modules/identity|推送相关边界]]
