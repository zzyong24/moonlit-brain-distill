# 《{anchor_name}思想体系》

<!--
模板使用说明（v2.0，assemble 阶段必须从实际产物动态填入，禁止硬编码）：

| 占位符              | 取值来源                                                            |
|---------------------|---------------------------------------------------------------------|
| {anchor_name}       | 创作者标识（如「示例创作者甲」「示例创作者乙」）                              |
| {N}                 | 原始材料数 = `ls claim-pool/*.yaml` 去重后的 video_id 个数         |
| {ISO8601}           | assemble 时刻的 ISO8601 时间戳                                    |
| {input_dir}         | 实际工作目录绝对路径                                               |
| {N_claims}          | `cat claim-pool/*.yaml \| grep -c "  - id:"`                      |
| {N_topics}          | `topics.yaml` 中 `^- topic:` 行数                                  |
| {N_cases}           | `grep -rc "^  - case:" chapters/*.yaml \| awk -F: '{s+=$2}'`       |
| {N_numbers}         | `grep -rc "^    - number:" chapters/*.yaml \| awk -F: '{s+=$2}'`   |
| {N_extended}        | `grep -c "【扩展】" chapters/*.md` （延伸思考段落数,v2.0 新增）    |

v2.0 新增渲染项：
- 每章正文前显示 chapter_type 标签（"基础概念/方法论/..."）
- 每章末尾显示延伸思考小节（extended_thinking 内容）
- 三类内容标签（【原话】/【蒸馏】/【扩展】）在正文中保留

⚠️ 历史教训：v2.0 时期 {N} 写成 45、v3.0 实际是 86，但模板里没有强约束，
assemble 步骤会直接复用旧文案，产出"标题写 45、内容按 86"的不一致。
本次 v2.0 修复在模板头部加入上表，强制 assemble 阶段按产物实际数量填入。
v2.0 进一步：auditor 子阶段（Phase 4.5）会先校验 7 项计数,只有全 pass 才
允许 assemble 启动,杜绝数字对不上的问题。
-->

> 本书由 moonlit-brain-distill skill 从 {N} 份原始材料蒸馏而成
> 生成时间：{ISO8601}
> 原始材料来源：{input_dir}
> 创作者：{anchor_name}
> Claim 总数：{N_claims} | Topic 总数：{N_topics} | 案例总数：{N_cases} | 数字与统计总数：{N_numbers} | 延伸思考段落：{N_extended}

> **内容标签说明**（v2.0 必读）：
> - **【原话】** = 作者在视频中逐字说过的话,标注 `> 出处: <video_id>`
> - **【蒸馏】** = 基于多个 claim 合成的判断,标注 `> 派生自: <claim_id 列表>`
> - **【扩展】** = AI 基于作者原则做的合理推断,或作者未明说的洞见,见每章末尾"延伸思考"独立小节

---

## 序：此人的大脑如何运作

{worldview.philosophy 完整呈现}

---

## 第一章：{ch1.title} `[chapter_type: {ch1.chapter_type}]`

{ch1.content}

### 延伸思考（AI 推断,非作者原话）

> 以下内容基于作者原则延伸,**不是作者说过的话**。每段都标注了依据,可放心参考但不要当作作者观点引用。

{ch1.extended_thinking}

---

## 第二章：{ch2.title} `[chapter_type: {ch2.chapter_type}]`

{ch2.content}

### 延伸思考（AI 推断,非作者原话）

{ch2.extended_thinking}

---

...（按章节序号排列,每章格式同上：title + chapter_type 标签 + content + extended_thinking）

---

## 附录 A：核心观点索引（按章节归组）

（**重要**：本附录按章节归组,每章下列出该章引用的所有 claim ID + 简述。完整原文见各章节。）

### 第一章：{ch1.title}
| ID | 核心观点 | 置信度 | 出处视频 |
|----|----------|--------|----------|
| {used_id} | {text[:50]}... | {conf} | {title} |

### 第二章：{ch2.title}
（同上结构）

...（每章一节）

### 未引用条目
- 剩余 {unreferenced_count} 条 claim 来自次要或边缘视频,列 ID + 极简描述（10 字内）

---

## 附录 B：反信仰清单

按强度排序（**前 12 条完整保留** + 后续按章节归组 + 末尾列出"次要反信仰"）：

### Top 12 强反信仰（完整保留 text + reason + 出处）
1. **{belief_1}**
   - 作者理由：{why_rejected 完整保留}
   - 出处：{title}（{video_id}）

...（共 12 条）

### 各章节反信仰（按章节归组,每章 ≤ 5 条精选）
#### 第一章：{ch1.title}
- {ab1.text}（理由：...）
- ...

### 次要反信仰（仅列 ID + 简述）
- {video_id}_ab{idx}: {text[:30]}...

---

## 附录 C：数字与统计

**按章节归组**,每章下分组列出该章用过的所有 numbers（不重复全 dump）：

### 第一章：{ch1.title}
| 数字 | 上下文 | 出处 |
|------|--------|------|
| ... | ... | ... |

（每章一节,**不重复**出现在多章的数字）

### 跨章节通用数字
（出现在多章的数字,单独列出避免重复）

---

## 附录 D：关键案例

**按章节归组**,每章列出该章引用的所有 case（不重复全 dump）：

### 第一章：{ch1.title}
#### {case.id}: {case.title 简述}
- **问题**：{problem 完整保留}
- **方案**：{solution 完整保留}
- **结果**：{result 完整保留}
- **出处**：{title}

...（每章一节,每章 ≤ 8 条精选）

---

## 附录 E：延伸思考汇总（v2.0 新增）

> **重要**：本附录是各章节 `extended_thinking` 内容的汇总索引,不是作者原话,是 AI 基于作者原则的合理推断。
> 引用时请明确标注"AI 延伸思考,非作者观点"。

| 章节 | 延伸思考主题 | 依据 |
|------|-------------|------|
| 第一章 | {ch1.extended_thinking_topics} | {claim_ids 或 "AI 推断"} |
| 第二章 | {ch2.extended_thinking_topics} | {claim_ids 或 "AI 推断"} |
| ... | ... | ... |

---

*本书由 moonlit-brain-distill skill v2.0（Creator Digital Brain Reconstruction Engine）生成。*
*质量由 Phase 4.5 Auditor（元数据）+ Phase 5 Critic（内容）双重闸门背书。*
*所有【原话】均可追溯到具体 video_id,所有【蒸馏】均可追溯到具体 claim_id,所有【扩展】均独立标注。*
