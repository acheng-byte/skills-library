---
name: fix-code-format
description: "运行 prettier 和 lint 工具修复代码格式问题，确保代码风格符合项目规范。触发词：代码格式、lint修复、prettier、格式化代码。"
---

# Fix Lint and Formatting

## Instructions

1. Run `yarn prettier` to fix formatting
2. Run `yarn linc` to check for remaining lint issues
3. Report any remaining manual fixes needed

## Common Mistakes

- **Running prettier on wrong files** - `yarn prettier` only formats changed files
- **Ignoring linc errors** - These will fail CI, fix them before committing