---
name: oss-contributor-swarm
description: "部署9智能体蜂群自动寻找开源项目的 good first issue 并提交 PR，支持持续贡献模式。触发词：开源贡献、自动PR、蜂群模式、OSS贡献。"
---

# OSS Contributor Swarm Skill

> "I don't just write PRs. I continuously contribute to open source while learning from every interaction."

This skill deploys a **9-agent swarm** that autonomously:
1. Qualifies repositories for contribution-friendliness
2. Finds suitable "good first issues"
3. Analyzes requirements
4. Clones and understands codebases
5. Writes fixes/features
6. Adds tests (if needed)
7. Updates documentation (if needed)
8. Creates professional pull requests
9. Responds to review feedback automatically

## Quick Start

# Run a single contribution cycle

# Run continuous 24/7 mode (target: 3-5 PRs/day)

# View dashboard
```

## Prerequisites

- `jq` and `git`

## Key Features

### Continuous Mode
Runs 24/7, automatically finding and contributing to open source projects.

### Learning System
Tracks success patterns and improves over time:
- Best issue types (typo, docs, tests)
- Best languages and repo sizes
- Maintainer preferences
- Auto-avoids problematic repos

### Auto-Fix Reviews
Agent 8 can automatically fix simple review requests:
- Typos, style, formatting → Auto-fix
- Logic changes → Escalate to human

## Configuration

Edit `config/swarm-config.json` to customize:
- Target daily PRs (default: 3-5)
- Max concurrent PRs (default: 3)
- Repo qualification criteria
- Learning parameters

## Safety

- Never force pushes
- Respects CONTRIBUTING.md
- Max 5 files / 100 lines per PR
- Human approval for security changes

See README.md for full documentation.