---
name: vc-daily-command-center
description: 投资人每日/每周指挥中心。用于用户要求今日作战地图、周度复盘、时间审计、工作流自动化清单、技能缺口地图、学习 ROI 排序、关系维护提醒、下周主题或把混乱任务变成高杠杆行动计划时。
metadata:
  short-description: 每日作战地图、周复盘和时间审计
---

`````
# VC Daily Command Center

## When to use
用于把投资人的任务、会议、关系维护、学习计划和复盘材料变成可执行作战地图。覆盖原 18 个用例中的 1、10、15、16。

## Input contract
优先读取用户给出的文本、Markdown、日历/会议摘要或文件路径。若输入不足，先基于已有信息输出“待补信息”列表，不要阻塞。

## Workflow
1. 识别场景：`daily` 今日作战地图、`weekly` 周复盘、`audit` 时间/工作流审计、`skill-gap` 技能缺口。
2. 提取硬约束：截止时间、会议、关系、资金/声誉底线、可用时间。
3. 用 Top 1 法则排序：如果只做一件事，锁定最高杠杆动作。
4. 标记 AI 可做、可委派、应删除、必须本人判断的动作。
5. 输出到 Markdown；如用户需要落盘，默认目录 `outputs/daily/` 或 `outputs/weekly-review/`。

## Output format
- 输入摘要
- 如果今天/本周只做一件事
- 时间块规划或复盘主题
- AI/委派/删除清单
- 高杠杆动作
- 关系维护动作
- 下一步 24 小时动作
- 可复用标签

## Templates and scripts
- 模板：`../../templates/daily-command-map.md`、`../../templates/weekly-review.md`、`../../templates/workflow-audit.md`、`../../templates/skill-gap-map.md`
- 落盘脚本：`../../scripts/new_report.py`
`````
