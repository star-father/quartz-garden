---
type: concept
title: concept-lora-finetuning（LoRA 高效微调）
tags: [domain/km, type/concept, status/done, domain/llm]
source: ""
create-time: 2026-07-30 15:08:55
created: 2026-08-01
updated: 2026-08-01
sources: [人工补充]
publish: true
---
# concept-lora-finetuning（LoRA 高效微调）

> **类型**: concept
> **创建时间**: 2026-07-30
> **最后更新**: 2026-07-30
> **来源**: [[raw/obsidian-creation-space/疯聊AI知识库/03_Knowledge/大模型/高效微调.md]] · [[raw/obsidian-creation-space/疯聊AI知识库/03_Knowledge/大模型/全量微调.md]]

## 摘要
LoRA（Low-Rank Adaptation）是最主流的 **PEFT（参数高效微调）** 方法：冻结预训练权重，仅用低秩矩阵旁路（ΔW = A×B，r<<d）更新少量参数（<1%），显存需求从 A100 80G+ 降到 24G 可用，效果接近全量微调。

## 详情

### 一、PEFT 核心思想
冻结预训练模型权重，只训练少量额外参数（Adapter / Prefix Tuning / P-Tuning v2 / LoRA / BitFit 等）。对比全量微调更新 100% 参数，PEFT 更新 <1%，成本骤降、灾难性遗忘风险低、可为不同任务维护不同权重。

### 二、LoRA 原理
- 原始权重 W(d×d)，增量 ΔW = A(d×r) × B(r×d)，其中 r<<d
- 推理时 W' = W + ΔW（可合并到原权重，无额外延迟）
- 优势：可训练参数减少 100x~1000x；与量化（QLoRA）结合进一步降显存；多任务只切换 LoRA 权重

### 三、LoRA vs 全量微调
| 维度 | 全量微调 | LoRA/PEFT |
|------|----------|-----------|
| 参数量 | 100% | <1% |
| 显存 | 极高(A100 80G+) | 低(24G 可用) |
| 速度 | 慢 | 快 |
| 效果 | 最佳 | 接近全量 |
| 遗忘风险 | 高 | 低 |
| 多任务 | 需整模型 | 切 LoRA 权重 |

### 四、常用工具
Hugging Face PEFT（官方库）、LLaMA-Factory（一站式微调平台）、Unsloth（加速+显存优化）、SWIFT（阿里）。

## 关联
- 相关概念: [[topic-llm-core-concepts]] — 大模型核心概念总览
- 相关 raw: [[raw/obsidian-creation-space/疯聊AI知识库/03_Knowledge/大模型/全量微调.md]] — 全量微调对比（待编译）
- 参见: [[topic-obsidian-ai-stack]]

## 引用来源
- [1] [[raw/obsidian-creation-space/疯聊AI知识库/03_Knowledge/大模型/高效微调.md]] — LoRA/PEFT 详解（编译源）
- [2] [[raw/obsidian-creation-space/疯聊AI知识库/03_Knowledge/大模型/全量微调.md]] — 全量微调对比（编译源）

## 变更记录
- 2026-07-30: 由空占位页编译为正式概念页，来源同上 raw 笔记
