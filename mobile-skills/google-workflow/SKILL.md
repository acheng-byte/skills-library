---
name: google-workflow
description: "使用 gws 命令行工具操作 Google Workspace 工作流，包括 standup 报告、会议准备、邮件转任务等。触发词：Google工作流、gws workflow、standup报告。"
---

# workflow (v1)

> **PREREQUISITE:** Read `../gws-shared/SKILL.md` for auth, global flags, and security rules. If missing, run `gws generate-skills` to create it.

gws workflow <resource> <method> [flags]
```

## Helper Commands

| Command | Description |
|---------|-------------|
| [`+standup-report`](../gws-workflow-standup-report/SKILL.md) | Today's meetings + open tasks as a standup summary |
| [`+meeting-prep`](../gws-workflow-meeting-prep/SKILL.md) | Prepare for your next meeting: agenda, attendees, and linked docs |
| [`+email-to-task`](../gws-workflow-email-to-task/SKILL.md) | Convert a Gmail message into a Google Tasks entry |
| [`+weekly-digest`](../gws-workflow-weekly-digest/SKILL.md) | Weekly summary: this week's meetings + unread email count |
| [`+file-announce`](../gws-workflow-file-announce/SKILL.md) | Announce a Drive file in a Chat space |

## Discovering Commands

Before calling any API method, inspect it:

# Browse resources and methods
gws workflow --help

# Inspect a method's required params, types, and defaults
gws schema workflow.<resource>.<method>
```

Use `gws schema` output to build your `--params` and `--json` flags.