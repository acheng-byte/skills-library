---
name: blog-monitor
description: "使用blogwatcher CLI工具追踪博客和RSS/Atom订阅源的更新，支持添加博客、扫描新文章、标记已读等操作。当用户提到博客监控、RSS订阅、追踪博客更新、博客阅读时触发。"
---

# blogwatcher

Track blog and RSS/Atom feed updates with the `blogwatcher` CLI.

Install
- Go: `go install github.com/Hyaxia/blogwatcher/cmd/blogwatcher@latest`

Quick start
- `blogwatcher --help`

Common commands
- Add a blog: `blogwatcher add "My Blog" https://example.com`
- List blogs: `blogwatcher blogs`
- Scan for updates: `blogwatcher scan`
- List articles: `blogwatcher articles`
- Mark an article read: `blogwatcher read 1`
- Mark all articles read: `blogwatcher read-all`
- Remove a blog: `blogwatcher remove "My Blog"`

Example output
```
Tracked blogs (1):

  xkcd
    URL: https://xkcd.com
```
```
Scanning 1 blog(s)...

  xkcd
    Source: RSS | Found: 4 | New: 4

Found 4 new article(s) total!
```

Notes
- Use `blogwatcher <command> --help` to discover flags and options.