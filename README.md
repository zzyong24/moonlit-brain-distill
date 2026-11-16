<p align="center"><img src="assets/readme/hero.png" width="100%" alt="月明·思想体系蒸馏概念插图"></p>

# 月明·思想体系蒸馏

把同一创作者的多份文本组织为带来源引用的思想体系 Markdown，保留世界观、方法论、心智模型与决策原则。正文区分【原话】和【蒸馏】，AI 延伸单独隔离为【扩展】。

这是本地 `brain-distill` v2.0 的可分发版本，保留既有六阶段流程，不含真实成书样例或私人材料。首图为 AI 生成的品牌概念插图，不是运行截图。

## 安装

把这句话交给支持 Skill 安装的 Agent：

> 请帮我安装这个 Skill：https://github.com/zzyong24/moonlit-brain-distill

也可以使用命令行安装器（安装器需要 Node.js，Skill 本身不需要）：

```bash
npx skills add zzyong24/moonlit-brain-distill
```

或克隆仓库后将整个目录放入宿主的 Skill 目录：

```bash
git clone https://github.com/zzyong24/moonlit-brain-distill.git
```

技术名称与安装目录保持 `moonlit-brain-distill`；宿主须具备下文依赖的能力。

## 使用

向 Agent 提供：

> 请使用 moonlit-brain-distill，把我有权使用的同一创作者文本目录整理为思想体系，保留来源，区分原话、蒸馏和扩展。

输入至少 3 份同一创作者的 `.txt`、`.md` 或 `.srt` 文本。输出为输入目录同级的 `<输入目录名>-思想体系.md`，中间结果包括 claim-pool、topics、worldview、chapters、audit_report 和 critic_report。

## 依赖与配置

本 Skill 是提示词、数据结构和模板，不内置模型调用器，无需单独配置 API Key。宿主须提供文件读写、文本生成和隔离子任务能力；没有子任务能力时本流程未验证。模型、费用和凭据由宿主配置决定，不提供自动计费兜底。

不下载或转写视频；批量逐字稿另见已公开的 [月明·抖音主播批量转写](https://github.com/zzyong24/moonlit-douyin-anchor-transcribe)。最终写入知识库依赖使用环境另行安装的 thirdspace-vault，且需要对应写入授权；仅生成本地成书不需要知识库连接。

## 数据边界

文本会发送给宿主选择的模型；请在使用前确认模型提供方和材料权限。本 Skill 不携带真实语料、Cookie、浏览器状态或凭据，不会自行下载素材。公开的 Skill 许可证不授予任何原始材料的转载权。

## 验证与限制

当前版本已通过文件结构、相对链接、严格校验、发布树秘密扫描和本地安装器安装检查；没有执行真实创作者蒸馏、独立效果评估或跨平台验收。模板中的 YAML 是结构说明，不是机器可执行的 JSON Schema。audit/critic 是执行者按提示词开展的检查，存在判断和计数错误风险，不能替代原文复核。

现有 v2.0 对主题数、章节数和每章引用数有下限，少量材料可能不足以满足；不能通过补造观点、数字或引用凑数。遇到这类冲突应报告输入不足并停止。当前不支持增量蒸馏，非中英文材料、直播滚动输入未验证。

入口及资源见 [SKILL.md](SKILL.md)。

## 来源与许可

来源：月明水清深自有的本地 brain-distill v2.0，作者已确认归属并选择 MIT，见 [LICENSE](LICENSE)。分发只调整名称、匿名示例和安装说明，保留原有 prompts、schemas 和成书模板。维护源集中在 Moonlit Skills，独立 GitHub 仓库为发布副本。素材和模型输出不因本 Skill 的 MIT 许可而自动获得使用授权。
