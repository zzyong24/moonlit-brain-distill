---
name: moonlit-brain-distill
description: 当用户要把**某创作者/博主/作者**的一批视频/文字材料蒸馏成"思想体系"（"把 @XXX 所有视频写成书"/"蒸馏思想体系"/"重建某人大脑"/"creator brain reconstruction"/"distill knowledge system"）时使用此 skill。从 1 份目录输入（含 .txt / .md / .srt 等任意文本文件）→ 重建此人的世界观、第一性原理、心智模型、方法论、决策原则、反信仰 → 输出一本《XXX思想体系.md》。运行方式：主 Agent 按 **6 阶段流水线**（claim 提取 → topic 聚类 → worldview → chapter → **auditor 元数据校验** → critic 内容审查）dispatch sub-agent。v2.0 新增三项硬规则：①正文必须区分【原话】/【蒸馏】/【扩展】三类内容 ②【扩展】必须物理隔离到 extended_thinking 区 ③Phase 4.5 auditor 校验元数据数字一致性。模型由宿主 Agent 决定，本 skill 不实现模型调度层。不用于：AI 摘要、知识卡片、视频下载（走 moonlit-douyin-anchor-transcribe）、AI 分析报告（走 dyd_analysis.py 主播蒸馏）。
license: MIT
---

# moonlit-brain-distill Skill (v2.0)

> **Brain Distill 不是一个 Summary Skill。它是一个 Creator Digital Brain Reconstruction Engine。**
> 输入 100 个视频，输出一个创作者完整的思想体系。

## 黄金规则

**禁止**：视频 → 摘要

**必须**：视频 → 观点(Claim) → 概念(Concept) → 方法论(Framework) → 心智模型(Mental Model) → 世界观(Worldview) → 知识体系(Knowledge Book)

始终追问：
- 作者相信什么？
- 作者反对什么？
- 作者如何做决策？
- 作者的第一性原理是什么？
- 作者的世界观是什么？

## v2.0 三项硬规则（相对 v1.0 的升级）

| # | 规则 | 实施位置 | 校验位置 |
|---|---|---|---|
| 1 | **正文三类内容显式标签** | `prompts/chapter-writer.md` | `prompts/critic.md` |
| 2 | **【扩展】必须隔离到 extended_thinking** | `prompts/chapter-writer.md` / `schemas/chapter.yaml` | `prompts/critic.md` external_knowledge_pollution |
| 3 | **元数据数字一致性** | `prompts/auditor.md`（新）/ `schemas/audit.yaml`（新） | `audit_report.yaml` verdict 闸门 |

### 三类内容定义

| 标签 | 来源 | source_refs |
|---|---|---|
| **【原话】** | transcript 逐字引用 | `[video_id]` |
| **【蒸馏】** | 基于多个 claim 的合成 | `[claim_id 列表]` |
| **【扩展】** | AI 的类比、推断、外部知识 | `null`（必须移到 extended_thinking） |

## 何时使用本 skill

- 用户说"把 @XXX 所有视频写成书"/"蒸馏思想体系"/"重建某人大脑"
- 用户提供 1 份含 ≥ 3 份文本文件的目录
- 用户说"creator brain reconstruction"

## 何时**不要**使用本 skill

- 单条视频 → 走 douyin-transcribe
- 批量主播逐字稿 → 走 moonlit-douyin-anchor-transcribe
- 账号运营分析报告 → 走主项目 dyd_analysis.py
- 知识卡片入库 → 走 thirdspace-vault（最终产物入库也走它）

## 输入识别（主 Agent 自判）

- 含 ≥ 3 份 .txt / .md / .srt 等文本文件
- 文本需是**同一创作者**、**内容性为主**（不是杂谈流水账）
- 不适合时主 Agent 明确提示，**不强行运行**

## 6 阶段工作流（主 Agent 按此串行调度）

### Phase 1: Claim 提取（★ 并行 sub-agent）

**主 Agent 应为每个文本文件启动独立的分析任务（sub-agent）。不要串行处理。**

读 `prompts/video-analyzer.md`，对每份文本派 1 个 sub-agent 跑：
- 输入：单份文本
- 输出：1 份 claims YAML（按 `schemas/claim.yaml` 结构）
- 汇成 `claim-pool/` 目录

### Phase 2: Topic 聚类

读 `prompts/topic-cluster.md`，主 Agent 单次跑：
- 输入：claim-pool/ 全部 YAML
- 输出：1 份 `topics.yaml`（按 `schemas/topic.yaml` 结构）

### Phase 3: Worldview 重建（最关键）

读 `prompts/worldview-agent.md`，主 Agent 单次跑：
- 输入：claim-pool/ + topics.yaml
- 输出：1 份 `worldview.yaml`

### Phase 4: Chapter 写作（v2.0 升级）

读 `prompts/chapter-writer.md`，主 Agent 单次跑：
- 输入：worldview.yaml + topics.yaml + claim-pool/ + `references/chapter-types.md`
- **v2.0 必填**：每章先选 `chapter_type`（6 选 1：基础概念/方法论/行业判断/案例分析/心境反思/哲学总纲）
- **v2.0 必填**：每章 `content_sections` 每节带 `type` 标签（原话/蒸馏/扩展）
- **v2.0 必填**：【扩展】全部进 `extended_thinking` 字段
- 输出：`chapters/chapter_NN.yaml`（按 `schemas/chapter.yaml` 结构）

### Phase 4.5: Auditor 元数据校验（v2.0 新增）

读 `prompts/auditor.md`，主 Agent 单次跑：
- 输入：input_dir
- 输出：`audit_report.yaml`（按 `schemas/audit.yaml` 结构）
- 校验 7 项：`materials_total / materials_success / materials_failed / materials_pending / claim_count / topic_count / chapter_count`
- **verdict=fail** → 回 Phase 1/2/4 修复,**不进入 Phase 5**
- **verdict=pass** → 进 Phase 5

### Phase 5: Critic 内容审查（v2.0 升级）

读 `prompts/critic.md`，主 Agent 单次跑：
- 输入：claim-pool/ + chapters/ + `audit_report.yaml`（必须 verdict=pass）
- 输出：`critic_report.yaml`
- **v2.0 新增 4 项检查**：
  - `external_knowledge_pollution`（【扩展】混入 content）
  - `unsourced_numbers`（无来源数字）
  - `topic_count_consistency`（章节 vs 主题引用覆盖率 ≥ 95%）
  - `voice_drift`（声调漂移,每章 ≤ 2 句）
- fail 时回 Phase 4 修订,**最多 2 轮**

## 最终输出

- 套 `templates/final-book.md` 模板
- 合并 chapters/ + worldview + topics + claim 索引
- 写入到输入目录**同级**：`<输入目录名>-思想体系.md`
- 入库：调 `thirdspace-vault` skill 写到 `03-知识/<子分类>/`

## 关键文件引用

- **6 个 prompt**：
  - `prompts/video-analyzer.md`
  - `prompts/topic-cluster.md`
  - `prompts/worldview-agent.md`
  - `prompts/chapter-writer.md` (v2.0)
  - `prompts/auditor.md` (v2.0 新增)
  - `prompts/critic.md` (v2.0)
- **4 个 schema**：
  - `schemas/claim.yaml`（v1.0 保留）
  - `schemas/topic.yaml`（v1.0 保留）
  - `schemas/chapter.yaml`（v2.0 新增）
  - `schemas/audit.yaml`（v2.0 新增）
- **1 个书模板**：`templates/final-book.md`（v2.0 加 extended_thinking 渲染）
- **2 个 references**：
  - `references/execution-flow.md`（v2.0 加 Phase 4.5）
  - `references/chapter-types.md`（v2.0 新增,6 种章节类型）

## 已知限制

- **不支持增量**（不接已蒸馏过的创作者再次增量）
- **只支持中英文**（v1）
- **不接直播 / 滚动 feed**
- **voice_drift 启发式检查**（v2.0 用关键词 + confidence 阈值,v2.1 计划上 embedding）

## 升级历史

- **v2.0**（2026-06-17）：新增三层内容标签 + auditor + 章节类型库 + critic 4 项新检查
- **v1.0**（2026-06-17）：首版,5 阶段流程
