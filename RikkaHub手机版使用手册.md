# RikkaHub 手机版完整使用手册
## 阿成专属 · 投资人工作流版

> 本手册覆盖：界面导航 / 18个提示词安装 / 世界书 / Agent Skills / MCP / Web搜索 / 文档生成工作流

---

## 一、界面全局导航

```
扩展管理
├── 快捷消息        → 一键发送的固定短句（如"总结一下" "继续"）
├── 提示词          → 自定义 AI 角色 + 系统指令
│   ├── 系统提示词   → AI 的角色设定（每次对话自动带入）
│   ├── 提示词注入   → 追加注入现有提示词（叠加增强）
│   │   ├── 模式注入 → 每次对话都自动追加的补丁
│   │   └── 世界书   → 根据关键词动态触发的上下文
│   ├── 预设聊天消息 → 对话开场白（AI 的第一句话）
│   └── 消息正则     → 发送/接收前自动替换文本
├── Agent Skills    → AI 可按需调用的工具技能包（仅支持 GitHub 导入）
├── MCP             → 连接外部工具和服务（Model Context Protocol）
└── Web 搜索        → 让 AI 联网实时搜索
```

---

## 二、提示词：三种使用方式

### 方式 A · 独立助手（推荐新手）

**场景**：为18个场景各建一个专属助手

**操作步骤：**
1. 主页 → 长按新建对话 → 选「创建助手」
2. 填写助手名称（如「赛道研究」「死亡笔记」）
3. 进入该助手 → 右上角设置 → 提示词
4. **系统提示词框**：粘贴对应的系统提示词（含 `{{nickname}}` 等变量）
5. 聊天内容模板：**保持默认** `{{ message }}` 不改
6. 保存

**使用时**：打开对应助手 → 发送用户消息模板（替换 `[方括号]`）

---

### 方式 B · 模式注入（叠加任意对话）

**场景**：给所有对话统一加一条规则（如「永远用中文」「去AI味」）

**操作步骤：**
1. 扩展管理 → 提示词 → 提示词注入 → 模式注入
2. 点「+ 添加模式注入」
3. 填写名称、内容
4. 位置选「**系统提示词后**」
5. 优先级：0（数字越大越优先）

**示例 · 去AI味注入（直接复制）：**

`````
回复时禁止：
- "当然可以" "当然" "好的，明白了" 等开场白
- "值得注意的是" "综上所述" 等套话
- 过多的感叹号和表情
- 排比句、"首先其次最后"的罗列格式
直接说结论，像专业人士对话，不像客服答题。
`````

---

### 方式 C · 世界书（智能触发，推荐进阶用户）

**世界书是什么：**

世界书（World Book）是一种**关键词触发式上下文注入**。当你在对话中输入特定关键词时，系统自动将对应的提示词片段注入到上下文中。

类似于「条件触发」：
- 你说「BP」→ AI 自动切换成 VC 死亡笔记分析框架
- 你说「研报」→ AI 自动进入研报极简压缩模式
- 你说「复盘」→ AI 自动启用周度复盘教练框架

**和模式注入的区别：**

| 功能 | 模式注入 | 世界书 |
|---|---|---|
| 触发条件 | 每次对话都触发 | 出现关键词才触发 |
| 适合场景 | 全局规则（去AI味、中文）| 场景切换（BP分析、研报） |
| 上下文占用 | 每次都有 | 按需加载，更节省 |

**操作步骤：**
1. 扩展管理 → 提示词 → 提示词注入 → 世界书
2. 添加条目
3. 设置触发关键词（支持多个关键词，用逗号分隔）
4. 填写触发后注入的内容
5. 设置位置（用户消息前/后）

**推荐配置（投资人版）：**

| 触发关键词 | 注入内容 |
|---|---|
| `BP` `项目` `创始人` | 死亡笔记分析框架（需求真伪+市场时机+死刑/投资逻辑） |
| `研报` `报告` `财报` | 研报极简压缩框架（结论前置+三块+假设检验） |
| `赛道` `行业` `帮我看看` | 产业研究框架（地图+成熟度+玩家+机会） |
| `复盘` `本周` `总结` | 周度复盘框架（模式识别+杠杆+能量审计） |
| `决策` `要不要` `纠结` | 决策压力测试（机会成本+最坏情景+72小时测试） |

---

## 三、18个投资人提示词 · 安装指南

### 推荐建立的 9 个常用助手

| 助手名称 | 对应提示词编号 | 使用频率 |
|---|---|---|
| 今日作战 | 01 | 每天早上 |
| 赛道研究 | 02 | 见项目前 |
| 想法整理 | 03 | 随时 |
| 死亡笔记 | 08 | 见项目前 |
| 研报压缩 | 09 | 读研报时 |
| 周度复盘 | 10 | 每周日晚 |
| 选题弹药 | 11 | 每周一早 |
| 爆款开头 | 12 | 写作前 |
| 工作流审计 | 16 | 月度 |

### 提示词配置界面详解（对照截图）

进入助手 → 设置 → 提示词，会看到以下几个区域：

**① 系统提示词**（必填）
粘贴 AI 的角色定义。支持所有 `{{变量}}` 。

**② 聊天内容模板**（不用改）
默认是 `{{ message }}`，这控制消息发给 AI 的格式。**保持默认不动**，除非你要做特殊的消息包装。

**③ 预设聊天消息**（选填，推荐用）
- `ASSISTANT` 那栏：AI 的**开场白**，对话一开始就自动出现，不需要你先发消息
- `USER` 那栏：预填一条假用户消息，用于 few-shot 示例（高级用法，一般不用填）
- 建议：只填 ASSISTANT 开场白，USER 留空

**④ 消息正则表达式**（可选）
对发送/接收的消息做文本替换，详见第九章。

---

### 安装 · 今日作战助手（完整示例）

**① 系统提示词（粘贴这个）：**

`````
你是执行力生产力教练，专注帮 {{nickname}} 构建清晰的每日执行地图。今天是 {{cur_date}}。

收到用户的任务清单后，输出以下四项：
1. 用"如果今天只做一件事"法则锁定 Top 1
2. 时间块规划（含缓冲区间，别排满）
3. 标出可丢给 AI 或委派的具体动作
4. 指出一个"高杠杆动作"——做了它，其他事会变简单或消失

语气直接，不废话，输出用 Markdown 列表格式。
`````

**② 聊天内容模板**：保持默认 `{{ message }}` 不动

**③ 预设聊天消息 → ASSISTANT 栏（选填，AI开场白）：**

`````
今天是 {{cur_date}}，{{nickname}}，把今天的任务扔给我，我帮你排出最高杠杆的执行顺序。
`````

**USER 栏**：留空

### 全部内置变量（截图确认版）

| 变量 | 说明 | 推荐场景 |
|---|---|---|
| `{{nickname}}` | 用户昵称（设置 → 个人信息填写） | 所有提示词都加 |
| `{{char}}` | 助手名称 | 让 AI 自称「我是XXX」 |
| `{{user}}` | 用户标识（同 nickname 的另一写法） | 备用 |
| `{{cur_date}}` | 今天日期（自动填入） | 今日作战、复盘 |
| `{{cur_time}}` | 当前时间 | 时间敏感场景 |
| `{{cur_datetime}}` | 日期+时间 | 完整时间戳 |
| `{{model_id}}` | 当前使用的模型 ID | 调试用 |
| `{{model_name}}` | 当前使用的模型名称 | 调试用 |
| `{{locale}}` | 语言环境（如 zh-CN） | 多语言切换 |
| `{{timezone}}` | 时区（如 Asia/Shanghai） | 跨时区场景 |
| `{{system_version}}` | 系统版本 | 调试用 |
| `{{device_info}}` | 设备信息 | 调试用 |
| `{{battery_level}}` | 电池电量 | 特殊场景 |

> **注意**：变量格式是 `{{变量名}}`，双大括号，无空格。聊天内容模板里的 `{{ message }}` 有空格是另一套语法，不要混用。

---

## 四、Agent Skills · 添加方式

> **注意**：Agent Skills 和提示词是两套体系。
> - Agent Skills 适合需要**调用外部工具**的场景（搜索、计算、数据查询等）
> - 提示词适合**纯文字分析和生成**，更稳定，手机端首选

### 方式 A · 手动粘贴（国内无 VPN 可用）

`raw.githubusercontent.com` 在国内被墙，GitHub 导入会报 `Failed to connect`。**用"+"粘贴是国内最稳的方案。**

1. 在 GitHub 上打开任意技能文件（见下方链接），复制全部内容
2. 进入扩展管理 → Agent Skills
3. 点右下角 **+** 按钮 → 「添加技能」
4. 在「SKILL.md 内容」框里粘贴内容
5. 点「保存」

### 方式 B · GitHub 导入（需要 VPN）

> 国内直接用会报 `Failed to connect to raw.githubusercontent.com`。
> 开 VPN 后可正常使用，每次导入一个技能。

1. 进入扩展管理 → Agent Skills
2. 点右下角 ↓（导入）按钮
3. 从下方「技能导入链接」找到想要的技能，复制 URL 粘贴进去
4. 点「导入」，等待下载完成

---

### 手机版技能导入链接（146个）

> 完整链接列表见：`mobile-skills/IMPORT-LINKS.md`
> 以下列出常用技能，开 VPN 后直接粘贴到导入框：

#### 学习与效率工具

**费曼学习法**（深度理解新概念）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/feynman-learning
```

**费曼备考法**（备考知识漏洞定位）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/feynman-exam
```

**刻意练习**（突破技能瓶颈）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/deliberate-practice
```

**主动回忆学习法**（自我测试代替重复阅读）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/active-recall
```

**间隔重复记忆**（科学间隔记忆卡片）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/spaced-repetition
```

**番茄工作法**（25分钟专注块规划）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/pomodoro-coach
```

**金山夜话**（人生对话·金山风格）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/jinshan-wisdom
```

#### VC投资工具

**VC投资人工作台**（BP分析/赛道研究/研报压缩）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/vc-investor-workbench
```

**今日作战地图**（每日任务高杠杆规划）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/vc-daily-command-center
```

**赛道研究**（六维产业框架）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/vc-sector-research
```

**项目死亡笔记**（投资逻辑/死因分析）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/vc-deal-memo
```

**A股价值分析**（个股估值/财务健康）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/a-share-value-analysis
```

#### AI与大模型

**深度研究专家**（多轮搜索综合研究）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/deep-research-expert
```

**提示词优化器**（改写提示词更精准）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/prompt-optimizer
```

**AI图像生成**（inference.sh 50+模型）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/ai-image-generation
```

**AI视频生成**（文字转视频）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/ai-video-generation
```

**音频转文字**（OpenAI语音识别）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/audio-to-text
```

#### 文档与文件处理

**Word文档生成**（JavaScript · docx库）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/word-document-processing
```

**Excel文件处理**（JavaScript · ExcelJS）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/excel-file-processing
```

**PPT文件生成**（JavaScript · pptxgenjs）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/ppt-file-processing
```

**PDF文件处理**（JavaScript · pdf-lib）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/pdf-file-processing
```

#### 内容与写作

**同金城写作风格**（财经自媒体风格）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/tong-jincheng-style
```

**SEO/AEO最佳实践**（搜索引擎优化）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/seo-aeo-best-practices
```

#### 工程与部署

**联网搜索**（AI驱动的Web搜索）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/web-search
```

**部署到Vercel**（Next.js/前端部署）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/deploy-to-vercel
```

**Supabase最佳实践**（后端/数据库）
```
https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/supabase-best-practices
```

---

> **完整146个技能链接**：查看仓库 `mobile-import` 分支下的 `mobile-skills/IMPORT-LINKS.md`

---

## 五、MCP · 连接外部工具

MCP（Model Context Protocol）让 AI 直接调用外部服务：数据库、文件系统、搜索引擎、API 等。

### 配置 MCP

1. 扩展管理 → MCP
2. 点「+ 添加 MCP 服务器」
3. 填写配置信息：

**配置字段说明：**

| 字段 | 说明 | 示例 |
|---|---|---|
| 名称 | 显示名称 | Brave Search |
| 类型 | 连接方式 | SSE / stdio |
| URL | 服务器地址 | https://mcp.example.com/sse |
| API Key | 认证密钥（如需要） | sk-xxx |

**常用 MCP 服务：**

| 服务 | 用途 | 说明 |
|---|---|---|
| Brave Search | 实时网络搜索 | 需要 Brave API Key |
| Notion | 读写 Notion 数据库 | 需要 Notion Integration Token |
| GitHub | 搜索代码和仓库 | 需要 GitHub Token |
| Obsidian | 读写本地 Obsidian 库 | 需要本地 MCP 服务器运行 |

### MCP vs Web 搜索的区别

| 功能 | MCP | Web 搜索 |
|---|---|---|
| 适合场景 | 调用特定服务/数据库 | 通用联网搜索 |
| 配置复杂度 | 需要服务器地址+认证 | 只需选择搜索引擎 |
| 功能范围 | 读写外部数据 | 只读，搜索结果 |

---

## 六、Web 搜索 · 让 AI 联网

### 开启 Web 搜索

1. 扩展管理 → Web 搜索
2. 选择搜索引擎（Google / Bing / Brave 等）
3. 填写 API Key（部分搜索引擎需要）
4. 保存并启用

### 使用方式

开启后，在对话中直接说：
- 「搜索一下XX的最新动态」
- 「联网查一下XXX现在的估值」
- 「帮我找一下这个赛道最近的融资新闻」

AI 会自动调用搜索，结合搜索结果回答。

### 推荐配置

| 搜索引擎 | 优点 | 备注 |
|---|---|---|
| Brave Search | 隐私保护，无广告 | 需要 Brave API Key（有免费额度） |
| Google | 结果最全 | 需要 Custom Search API |
| Bing | 中文支持好 | 需要 Azure Bing API Key |

---

## 七、手机端生成文档工作流

RikkaHub 手机端没有直接导出 MD 文件的按钮，但可以这样做：

### 流程一：让 AI 输出 Markdown 格式

在系统提示词中加一行：

`````
所有输出必须使用 Markdown 格式，包含适当的标题、列表和加粗。
`````

### 流程二：复制到笔记 App

1. 长按 AI 回复 → 复制全文
2. 粘贴到 Obsidian / 飞书文档 / 备忘录
3. 支持 Markdown 的 App 会自动渲染

### 流程三：让 AI 直接生成完整文档

**用户消息模板（直接发送）：**

`````
请生成一份完整的 Markdown 文档，包含以下内容：
[描述你要的文档内容]

格式要求：
- 用 # ## ### 层级标题
- 重要信息用 **加粗**
- 列表用 - 或数字
- 表格用 Markdown 表格格式
- 末尾附上「下一步行动」清单
`````

### 流程四：快捷消息 · 一键触发文档模式

在「快捷消息」里添加这条：

`````
把上面的内容整理成完整的 Markdown 文档，加上标题、目录和摘要，可以直接粘贴到 Obsidian 使用。
`````

---

## 八、快捷消息 · 推荐配置清单

在「扩展管理 → 快捷消息」添加以下快捷短句，提升效率：

| 名称 | 内容 |
|---|---|
| 整理成MD | 把上面内容整理成 Markdown 文档格式 |
| 继续 | 继续 |
| 更简洁 | 把回答压缩到原来的一半，去掉废话 |
| 给我数字 | 每个结论后面加一个支撑数据或案例 |
| 反方观点 | 现在站在反方，攻击你刚才说的观点 |
| 下一步 | 基于以上内容，给我3个可以立刻执行的下一步 |
| 去AI味 | 重写上面的内容，去掉所有AI腔，像真人说话 |
| 死亡笔记 | 现在用 VC 死亡笔记框架分析上面的项目，先说死因，再说投资逻辑 |
| 一页纸 | 把上面内容压缩成一页纸，结论前置，每项配数据 |

---

## 九、消息正则表达式 · 进阶用法

「消息正则表达式」可以在发送/接收消息前自动替换文本。截图中可以看到有一条已启用。

**字段说明：**

| 字段 | 说明 |
|---|---|
| 名称 | 这条规则的备注名 |
| 查找正则表达式 | 要匹配的正则 |
| 替换字符串 | 替换成什么（留空 = 删除） |
| 影响范围 User | 对你发出的消息生效 |
| 影响范围 Assistant | 对 AI 回复的消息生效 |
| 仅视觉 | 只影响显示，不影响实际发给 AI 的内容 |

**示例一 · 自动清理AI回复冗余开场白（勾 Assistant，不勾「仅视觉」）：**

```
查找：^(当然可以|好的，|当然，|非常好的问题[，。！]?)\s*
替换：（留空）
```

**示例二 · 自动给每条消息加格式指令（勾 User）：**

```
查找：(.+)
替换：$1\n\n请用 Markdown 格式输出，结论前置。
```

**示例三 · 仅视觉美化（勾「仅视觉」，只改显示不改实际）：**

```
查找：\*\*(.+?)\*\*
替换：【$1】
```

> **注意**：「仅视觉」打勾 = 只改你看到的样子，不影响 AI 实际收到的内容。不打勾 = 真实修改消息内容。

---

## 十、导入文件说明

配套文件路径：`outputs/RikkaHub导入格式/`

> **注意**：RikkaHub 每次只能导入单个 JSON 文件，不支持批量包。提示词（助手）目前没有导入格式，需手动新建助手并粘贴系统提示词。

### 1. 模式注入（6个全局规则）

路径：`RikkaHub导入格式/模式注入/`，每条规则一个独立 JSON 文件，逐一导入：

| 文件 | 默认状态 | 说明 |
|---|---|---|
| 01-去AI味-投资人简版.json | 启用 | 消除AI腔 |
| 02-结论前置.json | 启用 | 先结论后解释 |
| 03-投资人思维模式.json | 关闭 | 强制投资视角 |
| 04-强制Markdown输出.json | 关闭 | 格式化输出 |
| 05-全程中文.json | 关闭 | 强制中文 |
| 06-极简模式.json | 关闭 | 限制回复长度 |

### 2. 提示词注入-18个投资人技能（按需导入）

路径：`RikkaHub导入格式/提示词注入-18个投资人技能/`，每个场景一个 JSON，默认全部关闭，用哪个开哪个：

| 文件 | 场景 |
|---|---|
| 01-今日作战地图.json | 每日任务规划 |
| 08-项目死亡笔记.json | 项目/BP分析 |
| 09-研报极简压缩.json | 研报/长文压缩 |
| 10-周度复盘.json | 周复盘 |
| ...共18个 | 按需启用 |

### 3. 世界书-投资人关键词触发.json

包含6个关键词触发规则，默认启用「BP分析」「研报压缩」「赛道研究」「周复盘」：

| 触发词 | 自动注入 | 默认状态 |
|---|---|---|
| BP / 项目 / 创始人 | 死亡笔记框架 | 启用 |
| 研报 / 报告 / 财报 | 极简压缩框架 | 启用 |
| 赛道 / 行业 | 产业研究框架 | 启用 |
| 复盘 / 本周 / 总结 | 周复盘框架 | 启用 |
| 决策 / 纠结 | 压力测试框架 | 关闭 |
| 写文章 / 选题 | 内容策略框架 | 关闭 |

---

## 十一、推荐工作流 · 投资人日常场景

### 早上 8:00 · 今日作战

1. 打开「今日作战」助手
2. 发送：今日目标 + 任务清单（直接口语，不用格式）
3. AI 输出执行地图
4. 复制到备忘录 / Obsidian

### 见项目前 · 死亡笔记

1. 把 BP 关键信息（一段话）复制
2. 打开「死亡笔记」助手
3. 发送：`商业想法：[粘贴BP摘要]`
4. AI 输出7项分析
5. 截图存档，见面直接问

### 读研报时 · 研报压缩

1. 复制研报正文
2. 打开「研报压缩」助手（或直接在任意对话，有世界书触发）
3. 粘贴研报内容直接发送
4. AI 输出一页纸摘要 + 假设检验

### 周日晚 · 周度复盘

1. 打开「周度复盘」助手
2. 发送三条：做对的 / 搞砸的 / 新认知
3. AI 输出复盘分析
4. 发送快捷消息「整理成MD」
5. 复制到周报文件

---

## 十二、常见问题

**Q：系统提示词多长合适？**
A：500字以内效果最好。太长 AI 会忽略后半段。

**Q：{{nickname}} 没有显示我的名字怎么办？**
A：在 RikkaHub 设置 → 个人信息 里填写昵称「阿成」。

**Q：提示词注入和系统提示词有什么区别？**
A：系统提示词是针对这个助手的专属设定；注入是全局叠加，对所有对话都生效。

**Q：世界书和模式注入哪个更好？**
A：各有用途。模式注入适合「永远生效的规则」（去AI味、中文）；世界书适合「场景切换」（BP分析、研报），更节省上下文。

**Q：怎么知道系统提示词有没有生效？**
A：发一条「你现在的角色是什么？」，AI 应该能描述出你设置的角色。

**Q：Agent Skills 和提示词哪个更强？**
A：提示词更稳定，适合手机端。Agent Skills 适合需要调用外部工具（搜索、计算等）的场景，且只能从 GitHub 导入。

**Q：MCP 连不上怎么办？**
A：检查三点：1) URL 格式是否正确（SSE 类型一般以 /sse 结尾）；2) API Key 是否填写；3) 网络是否能访问该服务。

**Q：开启 Web 搜索后 AI 每次都搜索吗？**
A：不是。AI 会根据问题判断是否需要搜索。明确说「搜索一下」或「联网查」会强制触发搜索。

---

> 本手册版本：2026-05-03 v5
> 配套文件（路径：`outputs/RikkaHub导入格式/`）：
> - `系统提示词-MD版/` — 18个纯 MD，直接复制到 RikkaHub 系统提示词框
> - `模式注入/` — 6个全局规则 JSON（逐一导入）
> - `提示词注入-18个投资人技能/` — 18个场景注入 JSON（按需导入）
> - `世界书-投资人关键词触发.json` — 6个关键词触发（lorebook 格式）
> - `../18个投资人提示词-RikkaHub版.md` — 18个提示词完整内容（含用户消息模板）
>
> Agent Skills（GitHub 导入，需 VPN）：
> - 分支：`acheng-byte/skills-library` → `mobile-import` 分支
> - 完整导入链接：`mobile-skills/IMPORT-LINKS.md`（146个技能，每个单独链接）
> - 示例：`https://github.com/acheng-byte/skills-library/tree/mobile-import/mobile-skills/feynman-learning`
