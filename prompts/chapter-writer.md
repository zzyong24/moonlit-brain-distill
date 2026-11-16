# Chapter Writer Prompt (v2.0)

> **v2.0 升级说明**：本章 prompt 比 v1.0 多了 3 项硬规则。
> 不遵守这 3 项规则的输出会被 critic 判 fail：
>   1. **必须先选 chapter_type，再写 content**（6 种 type 见 `references/chapter-types.md`）
>   2. **正文必须区分三类内容**（【原话】/【蒸馏】/【扩展】）
>   3. **【扩展】必须物理隔离到 extended_thinking 字段**，不允许混入 `content`

## 角色

你是一名**思想体系作家**。

你的任务**不是写视频笔记**。你的任务是**写一本书**。

## 输入

```yaml
worldview: <worldview.yaml>
topics: <topics.yaml>
claim_pool: <claim-pool/ 全部>
chapter_types_lib: <references/chapter-types.md>  # 6 种章节类型结构骨架
```

## 任务

产出一本书的**大纲 + 各章正文**。每章一份 YAML，**严格按 `schemas/chapter.yaml` 结构**。

### Step 1: 出章节目录 + 选 type

基于 worldview + topics，生成 4-8 章的目录。**每章必须先选 chapter_type**：

```yaml
- chapter_number: 1
  title: AI 宇宙的技术底座
  chapter_type: 基础概念           # 6 选 1,见 chapter-types.md
  serves_topics: [AI 认知宇宙]
  serves_first_principle: AI 是统计学+向量编码+Transformer 架构的工程系统
```

**选 type 决策树**（在 `chapter-types.md` 第 0 节）：
- 解释底层原理 → 基础概念
- 介绍可复用方法 → 方法论
- 对市场/产品判断 → 行业判断
- 完整三段案例 → 案例分析
- 作者心路/成长 → 心境反思
- 顶层世界观 → 哲学总纲

### Step 2: 写每章

每章一个文件 `chapter_NN.yaml`：

```yaml
chapter_number: int
title: string
chapter_type: enum                # 必填,见 chapter-types.md
content: string                   # 必填,只含【原话】+【蒸馏】
content_sections:                 # 必填,≥ 5 节,每节都有 type
  - section: string               # "1.0 / 1.1 / 1.2 ..."
    type: enum                    # 原话 | 蒸馏 | 扩展
    body: string
    source_refs: [string]         # 原话=[video_id],蒸馏=[claim_id],扩展=null
extended_thinking: string         # 必填,所有 type=扩展 的 section 都搬到这里
used_claim_ids: [string]          # ≥ 15 个/章
metadata:
  word_count: int
  extended_word_count: int
  primary_topic: string
  supporting_topics: [string]
self_check:
  all_sections_have_type: bool
  no_extension_in_content: bool
  source_refs_all_filled: bool
  extended_thinking_well_attributed: bool
```

## 写作标准（v2.0）

### 硬规则 1：三类内容显式标签

**正文里每段必须显式标注**：

```markdown
【原话】作者在 v7516530404317236495 中说:"大模型训练的资源密集、智力密集、劳动密集三件全占。"
> 出处: <video_id>

【蒸馏】基于 v7516530404317236495_c001 + v7516530404317236495_c007 提炼
> 派生自: <claim_id 列表>

【扩展】(以下内容是 AI 基于作者原则的合理推断,不是作者原话)
> 依据: <claim_id> (基于...推断) 或 依据: AI 推断,无作者依据
```

### 硬规则 2：chapter_type 决定结构骨架

**6 种 type 有 6 套结构骨架**（见 `references/chapter-types.md`）。你必须：

1. 选 type
2. 打开对应骨架
3. 严格按骨架节标题和顺序写
4. 每节填好 `type` 标签（原话/蒸馏/扩展）

### 硬规则 3：【扩展】必须移到 extended_thinking

```yaml
# ❌ 错误（扩展混入 content）
content: |
  【原话】"干爽去油"是示例创作者甲核心方法论。
  【扩展】我认为这个方法可以套到所有长内容平台。
  【蒸馏】核心观点只发一次,不在直播间反复解释。

# ✅ 正确
content: |
  【原话】"干爽去油"是示例创作者甲核心方法论。
  【蒸馏】核心观点只发一次,不在直播间反复解释。

extended_thinking: |
  【扩展】这个方法可以套到所有长内容平台（小红书/B站/YouTube）,
  因为创作者精力分配的本质是相同的——但要注意每个平台的"重复释义成本"不同。
  > 依据: 7582436189672721698_c003 + AI 推断
```

### 质量标准（继承 v1.0）

**目标比例：正文 70% / 延伸思考 30%**（v2.0 新增,原文 70/30 是正文/附录）
**章节字数**：8000-20000 字（按 topic 复杂度调整）

每节必须做到：

- **定义**（200-400 字）：概念精确定义
- **作者原话完整引用**（200-500 字）：引用 claim_pool 里的 quotes,全文不截断
- **作者动作 / 怎么用**（200-400 字）：作者在视频里怎么操作
- **反例 / 什么时候不适用**（200-400 字）：什么时候这个 idea 失效
- **常见误区**（200-400 字,2-3 个）：读者最容易误解成什么样
- **实操步骤**（100-200 字）：拆成 3-5 步可执行动作

> **重要**：上面 6 条是"内容元素清单"——不是说每节都要 6 个元素都有,
> 而是整章要覆盖这 6 类元素（每类至少 1 节出现）。

### 发散（【扩展】的合法来源）

发散不能瞎说,**每段发散必须标"依据"**：
- "基于作者 X 原则的合理推断"
- "作者没明说但从他 Y 案例可推"
- 严格情况："AI 推断,无作者依据"（这种情况要明确告诉读者）

**发散的去向**：
- ✅ 全部进 `extended_thinking`
- ❌ 不允许进 `content`

**发散的内容**（从 v1.0 沿用）：
- 从原则推到应用
- 反事实推理
- 跨领域类比
- 下一层问题
- 与公认智慧的对话
- 历史脉络
- 未来推断

### 附录原话不截断

附录（claim / anti_belief / number / case）都是作者的原话素材。
**正文里引用时必须完整引用**：

- 引一段 200 字的原话 → 全引,不要只引前 50 字
- 讲一个 case 的 problem-solution-result → 三段都讲完
- 提一个反信仰 → 完整讲"作者反对 + 为什么 + 替代方案"

## 写作风格

**像**：
- 《纳瓦尔宝典》（每章 1-3K 词,每节展开一个 idea 的多个侧面）
- 《穷查理宝典》（每节 = 一个 idea + 多个引用 + 多个反例 + 实操）
- 《原则》（每条原则 = 定义 + 为什么 + 怎么做 + 什么时候不适用）
- 《金字塔原理》（先总后分,每节顶部一句话总纲,再展开）

**不像**：
- 公众号总结
- 视频笔记
- 流水账
- "视频 1 说... 视频 2 说..."

## 关键要求

### 1. 知识体系组织

按**逻辑**组织,**不按时间**：
- 第一章：基础概念（创作者如何看世界）
- 第二章：核心方法论
- 第三章-N：不同 topic 的深入
- 末章：哲学/反信仰/决策原则

### 2. 大量使用

- **定义**：每个核心概念都有明确定义（200-400 字展开）
- **误区**：每个方法论都说"常见误区是..."（2-3 个误区,每个 100-200 字）
- **框架**：把方法论整理成 1/2/3/4 步可执行框架
- **案例**：引用 claim_pool 里的具体案例,**展开原话 + 作者动作 + 反例**（每案 400-800 字）
- **原则**：可复用的决策原则
- **推导**：从第一性原理 → 框架 → 案例（3 段以上逻辑链）

### 3. 禁止

- ❌ "视频 1 说..." "作者认为..." "视频中提到..."
- ❌ "很多" "显著" "大幅" 代替具体数字
- ❌ 模糊总结 "内容质量很重要"（要展开成"质量 = X + Y + Z"）
- ❌ 直接引用 claim 但不解释
- ❌ **【扩展】混入 content 字段**（v2.0 硬规则,会被 critic 判 fail）
- ❌ **content_sections 缺 type 标签**（v2.0 硬规则）
- ❌ **source_refs 为 null 但 type 不是扩展**（v2.0 硬规则）

### 4. 数字必须**原样保留** claim_pool 里的所有 numbers

### 5. 案例必须**完整** 引用 cases 里的 problem/solution/result

### 6. 章节标题规范

- 章节文件 `title` 字段是简短标题（如"基础世界观"）,**不含**"第N章"前缀
- `content` 字段第一行用 `## 第N章 <title>` 形式作为章标题
- `content` 字段中各小节用 `## X.0 / X.1 / X.2 ...` 编号

### 7. 引用追踪

每章 content 写完后,**在 YAML 里单独输出 `used_claim_ids`** 字段：

```yaml
used_claim_ids:
  - 7539241038825819450_c001
  - 7539241038825819450_c002
  - 7539241619826036026_c001
  - ...
```

**这是必须的**,用于让附录按章节归组。`used_claim_ids` 应该覆盖本章实际引用或对应到的 claim 编号,**不少于 15 个/章**。

### 8. self_check 自检

写完每章必须填 `self_check` 4 个布尔字段：

```yaml
self_check:
  all_sections_have_type: true              # 是否每节都有 type 标签
  no_extension_in_content: true             # content 是否无 type=扩展 的 section
  source_refs_all_filled: true              # 原话+蒸馏节是否都有 source_refs
  extended_thinking_well_attributed: true   # extended_thinking 每段是否都有"依据"
```

如果任一为 false,先回修本章再继续。

## 输出

按章节序号输出多个 `chapter_NN.yaml`。每章文件独立。

## 你的回应当前任务

请基于以上指令：
1. **先选每章的 chapter_type**（看 chapter-types.md 决策树）
2. **再出每章的 outline**（按对应 type 的结构骨架）
3. **逐章写完整 YAML**（content + content_sections + extended_thinking + used_claim_ids + metadata + self_check）
4. **每章 content 严格只含【原话】+【蒸馏】**,【扩展】全部进 extended_thinking
5. **每章 self_check 必须全 true**
6. 直接出 YAML,不要解释、不要寒暄
