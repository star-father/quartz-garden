---
title: Obsidian PARA + 知识投影体系
type: method
created: 2026-08-08
source: 企业微信碎片（2026-08-08 11:16–11:17 集中投喂 96 张，img_048/049/044/084/082/043）
tags:
  - type/method
  - topic/obsidian
  - topic/knowledge-management
  - source/wechat
  - status/done
  - domain/km
status: 已入库
publish: true
---

# Obsidian PARA + 知识投影体系

> 来源：微信碎片 img_048/049（电脑文件管理 + Obsidian 知识投影 + 完整 PARA）、img_044（多设备同步先 pull 再改）、img_084（文件存放规范/标签/MOC/Dataview）、img_082（10 个核心插件）、img_043（5 个 Obsidian 插件）。
> 核心思想：**电脑保存真实文件，Obsidian 是原始文件的「知识投影」与思考工作台**——不是把文件搬进 Obsidian，而是让 Obsidian 成为所有内容的语义层。

## 一、整体架构：电脑 ↔ Obsidian 的双层

- **电脑**：保存所有真实文件（项目原件、源视频、封面、设计稿、代码、素材）。
- **Obsidian**：原始文件的「知识投影」与思考工作台（灵感、需求、素材、结论、卡片笔记）。
- **原则**：电脑保真实文件，Obsidian 保管理解、关联、初稿；AI 只读写 Obsidian（可选 AI 处理）。

## 二、PARA 分区（img_048/049）

| 区 | 含义 | 内容 |
|---|---|---|
| 0. 项目区 | 进行中的项目 | 视频制作、版本记录、复盘、发布清单 |
| 1. 长期学习区 | 持续学习主题 | — |
| 2. 生活/工具区 | 生活与工具 | — |
| 3. 完结归档素材库 | 已完结可复用素材 | 视频素材、研究证书、过刊图片、静频、视频 |

## 三、Obsidian 内部目录（img_048/049/084）

```
10_Raw_Inputs / 10_原稿      原始素材的转写、梳理、草稿
  灵感库(中转站)             灵感、截图、未成文想法，先接住不急着分类
12_预处理-概览               语音转写、文章化、摘要/洞察
30_Human_Notes              卡片笔记、自己的判断与洞见、复盘
20_ALWiki                   概念、人物、问题、教材、碎片的知识来源链接
60_选题+待办                把灵感/卡片/知识组为可证的沙盘；定受众/角度/素材/口吻
00_系统支持                 模板、规范、Skills、命名与索引
40_脚本库(输出)            归档与可复用素材（绑项目、素材库、备份）
50_项目库(输出)            制作计划、版本记录、复盘、发布清单
```

## 四、多设备同步纪律（img_044）

- 多台设备用同一仓库时：**先 Pull 再改、改完 Backup**——避免冲突。
- 同步与备份：应用设置、依赖、系统隐藏文件索引（可选 AI 处理）；iCloud/网盘/Time Machine 保底。

## 五、文件存放规范（img_084）

- 标签体系：`@tags`、`@date`、类型标签。
- MOC（内容概览）：今日思考、项目总览。
- Dataview 查询示例。
- 迁移说明、同步说明。

## 六、核心插件（img_082/043）

| 插件 | 用途 |
|---|---|
| Excalidraw | 手绘/白板图 |
| Templater | 模板填充 |
| Dataview | 数据库式查询 |
| defuddle | 网页转干净 Markdown |
| obsidian-markdown | Markdown 增强 |
| json-canvas | Canvas JSON 支持 |
| obsidian-bases | 结构化库（Bases） |
| obsidian-cli | 命令行操作 |

## 七、与本知识库的对照

本库 `D:\work_place\place` 已是这套思想的实践版：

- `raw/` = 原稿/灵感库（中转站，`raw/intake/wechat/` 接微信碎片）；
- `03_Knowledge/` = ALWiki（概念/方法/主题）；
- `01_Daily/` = Human_Notes（卡片/复盘）；
- `04_Projects/` = 项目区；
- `index.md` + `log.md` + `Output/` = MOC + 产出层；
- `scripts/` = 脚本库（输出）。

差异：本库用 `link_lint.py` + `add_frontmatter.py` 做自动化健康检查，比纯手动规范更稳。

## 关联

- 闭环方法论：[[03_Knowledge/methods/method-ai-tool-combo-loop]]
- 知识库四标准：[[03_Knowledge/methods/method-kb-four-standards-reuse]]
- 碎片池：[[raw/intake/wechat/wechat-2026-08-08]]
