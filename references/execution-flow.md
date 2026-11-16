# 6 阶段执行流（v2.0）

```
输入: <input_dir>/*.txt  (≥ 3 份)
  │
  ↓ 【Phase 1: Claim 提取】  ★ 并行
  主 Agent 读 prompts/video-analyzer.md
  派 N 个 sub-agent（每份 1 个）
  每个 sub-agent 产 1 份 claims.yaml
  ↓
  汇成 claim-pool/
  │
  ↓ 【Phase 2: Topic 聚类】
  主 Agent 读 prompts/topic-cluster.md
  读 claim-pool/ 全部
  产 topics.yaml
  │
  ↓ 【Phase 3: Worldview 重建】
  主 Agent 读 prompts/worldview-agent.md
  读 claim-pool/ + topics.yaml
  产 worldview.yaml
  │
  ↓ 【Phase 4: Chapter 写作】  🆕 v2.0 升级
  主 Agent 读 prompts/chapter-writer.md
  读 worldview + topics + claim-pool + chapter-types.md
  🆕 每章先选 chapter_type（6 选 1）
  🆕 content_sections 每节带 type 标签
  🆕 【扩展】全部进 extended_thinking
  产 chapters/chapter_NN.yaml
  │
  ↓ 【Phase 4.5: Auditor 元数据校验】  🆕 v2.0 新增
  主 Agent 读 prompts/auditor.md
  跑 7 项硬校验：
    1. materials_total
    2. materials_success
    3. materials_failed
    4. materials_pending
    5. claim_count
    6. topic_count
    7. chapter_count
  产 audit_report.yaml
  │
  ↓
  verdict=pass  ─────────────────┐
  verdict=fail → 回 Phase 1/2/4  │（不进入 Phase 5）
  修复后重跑 4.5                │
                                ↓
  ↓ 【Phase 5: Critic 内容审查】  🆕 v2.0 升级
  主 Agent 读 prompts/critic.md
  读 claim-pool/ + chapters/ + audit_report.yaml（必须 pass）
  🆕 4 项新检查：
    - external_knowledge_pollution
    - unsourced_numbers
    - topic_count_consistency
    - voice_drift
  产 critic_report.yaml
  │
  ↓
  verdict=pass → 进 final 输出
  verdict=fail → 回 Phase 4 修订（最多 2 轮）
  │
  ↓
  套 templates/final-book.md
  ↓
  输出: <input_dir同级>/<input_dir名>-思想体系.md
  ↓
  调 thirdspace-vault skill 入库
```

## 关键检查点

- **Phase 1 完成时**：claim-pool/ 应有 N 份 YAML
- **Phase 2 完成时**：topics.yaml 包含 5-15 个 topic
- **Phase 3 完成时**：worldview.yaml 6 个字段都非空
- **Phase 4 完成时**：chapters/ 包含 4-8 个 chapter_NN.yaml,每章含 chapter_type / content_sections / extended_thinking / self_check
- **Phase 4.5 完成时**（v2.0 新增）：audit_report.yaml verdict = pass（7 项全过）
- **Phase 5 完成时**：critic_report.yaml verdict = pass（9 项全过,v1.0 5 项 + v2.0 4 项）
- **最终**：<name>-思想体系.md 存在 + 调 thirdspace-vault 入库

## 回退矩阵（v2.0）

| 失败来源 | 回退到 | 原因 |
|---|---|---|
| Phase 4.5: materials_* | Phase 1 | claim 提取不完整 |
| Phase 4.5: claim_count | Phase 1 | claim 提取有缺漏 |
| Phase 4.5: topic_count | Phase 2 | topic 聚类没收敛 |
| Phase 4.5: chapter_count | Phase 4 | 章节数不在 4-8 范围 |
| Phase 5: external_knowledge_pollution | Phase 4 | 【扩展】混入 content |
| Phase 5: unsourced_numbers | Phase 4 | 数字无 claim 支撑 |
| Phase 5: topic_count_consistency | Phase 4 | used_claim_ids 没覆盖 topic 范围 |
| Phase 5: voice_drift | Phase 4 | 章节声调漂移 |
| Phase 5: hallucination | Phase 4 | 段落与 claim 库对不上 |
| Phase 5: missing | Phase 1/4 | 重要 claim/数字/案例缺失 |

## 升级历史

- **v2.0**（2026-06-17）：6 阶段（+Phase 4.5 auditor）,9 项 critic 检查
- **v1.0**（2026-06-17）：5 阶段,5 项 critic 检查
