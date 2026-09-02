---
title: 双向链接
type: concept
tags: [type/concept, status/done, domain/km]
source: ""
create-time: 2026-07-31 13:43:16
created: 2026-08-01
updated: 2026-08-04
sources: [人工补充]
publish: true
---
# 双向链接

> **类型**: concept
> **创建时间**: 2026-07-30
> **最后更新**: 2026-07-30
> **来源**: 🤖 AI 综合推理（通用 PKM 知识）+ 合并自 concept-双链、concept-链接（原为空占位页，Jaccard≥0.90 判定为强重复，已于 2026-08-04 并入本页并删除残桩）

## 摘要
双向链接（bidirectional link）是双向链接型笔记工具（如 Obsidian、Roam Research）的核心机制：在笔记 A 中插入指向笔记 B 的 `wikilink`，系统在 B 的"反向链接（backlink）"面板自动显示 A 的引用，无需手动维护两端。它是构建"知识网络"而非"文件夹层级"的关键，使原子笔记可跨主题相互连接。

## 详情

### 工作原理
- **正向链接**：编辑器中用 `笔记名` 创建指向目标笔记的链接。
- **反向链接**：目标笔记自动聚合并显示所有指向它的来源，形成"谁引用了我"的视图（Obsidian 的 Backlinks / Graph View）。
- **图谱视图（Graph View）**：将所有链接可视化为节点网络，揭示知识簇、枢纽节点与孤立节点。

### 在个人知识库中的作用
- 打破文件夹层级限制，让同一则笔记按多个主题被复用引用。
- 配合"原子化笔记 + MOC（内容地图）"形成可漫游的知识网（见 [[03_Knowledge/topics/topic-知识管理闭环方法论]]）。
- 本库 Ingest 工作流强制要求：新建/更新页面时必须建立双向 `wikilink` 交叉引用（见 [[WIKI-SCHEMA]]）。

### 与相邻概念的关系
- **双链** ≈ **双向链接**：同一概念的不同称呼，本文已合并两页。
- **链接**：泛指笔记间的引用关系；双向链接是其具体实现形态（自动维护反向侧）。

## 关联
- 相关主题: [[03_Knowledge/topics/topic-知识管理闭环方法论]] — 组网层用双链构建知识网络
- 相关主题: [[03_Knowledge/topics/topic-ai-knowledge-base-design]] — KB-Vault 用双链互连
- 参见: [[WIKI-SCHEMA]] — Ingest 强制双向链接规则

## 引用来源
- [1] 🤖 AI 综合推理 — 通用 PKM（Personal Knowledge Management）领域关于 bidirectional linking 的共识性定义，非来自单一 raw 源
- [2] [[03_Knowledge/topics/topic-知识管理闭环方法论]] — 组网层双链实践（line 25、42）
- [3] [[03_Knowledge/topics/topic-ai-knowledge-base-design]] — KB-Vault 双链互连（line 32）

## 变更记录
- 2026-07-30: 初始占位页创建（`concept-双向链接` / `concept-双链` / `concept-链接` 三份相同空占位）
- 2026-07-30: 合并三份占位页为本文（Jaccard≥0.90，依据 Output/raw-dedup-scan-2026-07-30），充实为正式概念页；删除另两页并修正 2 处引用
