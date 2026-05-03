---
name: vc-investor-workbench
description: VC Copilot 总控技能。用于用户说 VC Copilot、投资工作台、分析 BP、赛道研究、研报压缩、死亡笔记、今日作战地图、周复盘、内容弹药库、报价打磨但未指定具体 skill 时，负责识别意图、选择对应工作流、读取本地文件/网页输入并按统一 Markdown 输出规范落盘。
metadata:
  short-description: VC Copilot 总控路由和输出规范
---

`````
# VC Investor Workbench

## Role
这是 VC Copilot 的总控 skill。用户不需要记住具体 skill 名；你负责路由到合适工作流，并保持统一输入/输出规范。

## Routing map
- 今日作战地图、周复盘、时间审计、技能缺口 → `vc-daily-command-center`
- 赛道研究、技术入门、研报压缩、产业地图 → `vc-sector-research`
- BP 分析、死亡笔记、尽调问题、重大决策、用户理解 → `vc-deal-memo`
- 碎片想法、逻辑骨架、反方视角、缺口扫描 → `vc-thinking-refiner`
- 写作 polish、内容选题、开头、个人 IP、报价、销售文案 → `vc-content-engine`

## Operating rules
1. 先识别输入来源：粘贴文本、文件路径、网页 URL、Obsidian 笔记、PDF/Markdown。
2. 若涉及“最新/实时/当前”，必须使用联网或浏览器核查。
3. 原始材料和衍生报告分开保存；不要覆盖用户原文件。
4. 默认输出 Markdown，目录遵守 `outputs/<type>/YYYY-MM-DD-主题-工作流.md`。
5. 输出必须包含：输入摘要、核心判断、关键证据、反方视角、下一步动作、可复用标签。
6. 自动化、飞书、日历、邮件只做可插拔增强；除非用户明确要求，不主动创建自动化或写入外部系统。

## 18 use-case mapping
1 daily clarity → daily command center；2 sector research → sector research；3 messy thinking → thinking refiner；4 learning topic → sector research；5 decision stress test → deal memo；6 writing polish → content engine；7 deadlock → deal memo；8 project death note → deal memo；9 report distillation → sector research；10 weekly review → daily command center；11 content ammo → content engine；12 hooks → content engine；13 user anatomy → deal memo；14 offer polish → content engine；15 skill gap → daily command center；16 workflow audit → daily command center；17 personal IP → content engine；18 sales copy → content engine。

## Templates and scripts
- 总输出模板：`../../templates/base-output.md`
- 新建报告：`../../scripts/new_report.py`
- 提取输入：`../../scripts/extract_input.py`
- 插件校验：`../../scripts/validate_plugin.py`
`````
