---
name: vc-deal-memo
description: 投资项目判断技能。用于分析 BP、创业项目、被投企业问题、重大决策、用户画像、死亡笔记、尽调问题、需求真伪、市场时机、竞争替代、收入幻觉、团队基因、决策压力测试和系统杠杆点时。
metadata:
  short-description: BP 分析、死亡笔记和决策压力测试
---

# VC Deal Memo

## When to use
用于“先验尸，再谈感情”：把项目描述、BP、创始人故事或被投企业死局转成投资判断。覆盖原 18 个用例中的 5、7、8、13。

## Workflow
1. 提取项目一句话：客户、痛点、方案、商业模式、当前阶段。
2. 做死亡笔记：需求真伪、市场时机、竞争替代、收入幻觉、团队基因。
3. 做系统拆解：症状 vs 病根、利益方、杠杆点、24 小时速赢动作。
4. 做用户解剖：凌晨三点焦虑、付费触发点、信息 diet、反对声音。
5. 如是个人/组织重大决策，加入机会成本、最坏情景、信息缺口、72 小时测试。
6. 最后给“拒绝最硬理由”和“若投资最赌的点”。

## Output format
- 输入摘要
- 一句话项目/决策定义
- 核心判断
- 死亡笔记
- 系统图和杠杆点
- 用户/客户解剖
- 尽调追问清单
- 一句话死刑
- 一句话投资
- 下一步动作
- 可复用标签

## Templates and scripts
- 模板：`../../templates/deal-death-note.md`、`../../templates/decision-stress-test.md`、`../../templates/user-anatomy.md`
- 文件提取：`../../scripts/extract_input.py`
- 报告生成：`../../scripts/new_report.py`

