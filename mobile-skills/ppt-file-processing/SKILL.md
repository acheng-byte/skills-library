---
name: ppt-file-processing
description: "在手机端使用纯 JavaScript 生成 HTML 幻灯片演示文稿，适用于 RikkaHub QuickJS 环境（无需 npm）。当用户说「PPT制作」「制作幻灯片」「生成演示文稿」「演示文稿」时触发。"
---

# PPT 幻灯片制作（QuickJS 手机版）

## 环境说明

RikkaHub 使用 **QuickJS** 引擎执行 JavaScript，**无法使用 `require()`、npm 包、`fs` 模块或任何 Node.js API**。

**解决方案：生成自包含的 HTML 幻灯片文件。**

- QuickJS 生成完整 HTML 字符串（含内嵌 CSS 与 JS），通过 `console.log()` 输出
- 用户复制输出内容，保存为 `.html` 文件
- 在手机或电脑浏览器中打开，全屏查看，方向键翻页
- 在浏览器中选择「打印」→「另存为 PDF」即可分享

**流程：** `QuickJS 输出 HTML` → `保存为 .html` → `浏览器全屏查看` → `打印/导出 PDF`

---

## 主题配置

| 主题名 | 背景色 | 标题色 | 正文色 | 强调色 |
|--------|--------|--------|--------|--------|
| `dark`（深色）| `#1a1a2e` 深蓝黑 | `#e0e0e0` | `#b0b0b0` | `#4fc3f7` 蓝 |
| `light`（浅色）| `#ffffff` 白 | `#1a1a2e` | `#333333` | `#1565c0` 蓝 |
| `corporate`（商务）| `#f5f7fa` 浅灰 | `#0d2d6e` 深蓝 | `#333333` | `#1976d2` 蓝 |

---

## 幻灯片类型说明

| 类型 | 用途 |
|------|------|
| `cover` | 封面页（大标题 + 副标题 + 日期）|
| `content` | 内容页（标题 + 要点列表）|
| `two-column` | 双栏页（左右两列内容对比）|
| `chart` | 数据图表页（CSS 横向条形图）|
| `closing` | 结尾页（致谢/联系方式）|

---

## 核心函数：buildHTMLSlides()

```javascript
function buildHTMLSlides(config) {
  const {
    title = '演示文稿',
    theme = 'corporate',
    slides = []
  } = config;

  const themes = {
    dark: {
      bg: '#1a1a2e', slideBg: '#16213e', titleColor: '#e0e0e0',
      bodyColor: '#b0b0b0', accentColor: '#4fc3f7', mutedColor: '#7a8aaa',
      borderColor: '#2a3a5e', bulletColor: '#4fc3f7'
    },
    light: {
      bg: '#f0f0f0', slideBg: '#ffffff', titleColor: '#1a1a2e',
      bodyColor: '#333333', accentColor: '#1565c0', mutedColor: '#777777',
      borderColor: '#e0e0e0', bulletColor: '#1565c0'
    },
    corporate: {
      bg: '#dde3ea', slideBg: '#f5f7fa', titleColor: '#0d2d6e',
      bodyColor: '#333333', accentColor: '#1976d2', mutedColor: '#666666',
      borderColor: '#ccd5e0', bulletColor: '#1976d2'
    }
  };

  const t = themes[theme] || themes.corporate;

  const css = `
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body {
      background: ${t.bg};
      font-family: Arial, "Microsoft YaHei", sans-serif;
      display: flex; flex-direction: column;
      align-items: center; justify-content: center;
      min-height: 100vh;
      user-select: none;
    }
    #deck { position: relative; width: 100%; max-width: 960px; }
    .slide {
      display: none;
      background: ${t.slideBg};
      aspect-ratio: 16/9;
      padding: 48px 60px;
      position: relative;
      overflow: hidden;
      border-radius: 4px;
      box-shadow: 0 8px 32px rgba(0,0,0,0.25);
    }
    .slide.active { display: flex; flex-direction: column; justify-content: center; }

    /* 封面页 */
    .slide.cover { text-align: center; align-items: center; }
    .slide.cover .cover-title {
      font-size: clamp(28px, 5vw, 52px);
      font-weight: bold; color: ${t.titleColor};
      margin-bottom: 16px; line-height: 1.2;
    }
    .slide.cover .cover-subtitle {
      font-size: clamp(14px, 2.5vw, 24px);
      color: ${t.mutedColor}; margin-bottom: 8px;
    }
    .slide.cover .cover-meta {
      font-size: clamp(11px, 1.8vw, 16px);
      color: ${t.mutedColor}; margin-top: 24px;
    }
    .slide.cover .accent-bar {
      width: 80px; height: 4px;
      background: ${t.accentColor};
      margin: 16px auto;
    }

    /* 内容页 */
    .slide-title {
      font-size: clamp(20px, 3.5vw, 36px);
      font-weight: bold; color: ${t.titleColor};
      margin-bottom: 8px;
      border-bottom: 3px solid ${t.accentColor};
      padding-bottom: 10px;
    }
    .bullet-list { list-style: none; margin-top: 16px; flex: 1; }
    .bullet-list li {
      font-size: clamp(12px, 2vw, 20px);
      color: ${t.bodyColor};
      padding: 6px 0 6px 28px;
      position: relative; line-height: 1.5;
    }
    .bullet-list li::before {
      content: "▸";
      color: ${t.bulletColor};
      position: absolute; left: 0; font-size: 1em;
    }
    .bullet-list li.sub {
      font-size: clamp(11px, 1.7vw, 17px);
      color: ${t.mutedColor};
      padding-left: 52px;
    }
    .bullet-list li.sub::before { content: "–"; left: 32px; }

    /* 双栏页 */
    .two-col { display: flex; gap: 24px; flex: 1; margin-top: 16px; }
    .col {
      flex: 1; background: ${t.borderColor};
      border-radius: 6px; padding: 20px;
    }
    .col-title {
      font-size: clamp(13px, 2vw, 20px);
      font-weight: bold; color: ${t.accentColor};
      margin-bottom: 12px;
    }
    .col p, .col li {
      font-size: clamp(11px, 1.7vw, 17px);
      color: ${t.bodyColor}; line-height: 1.6;
    }
    .col ul { list-style: disc; padding-left: 18px; }
    .col li { margin-bottom: 6px; }

    /* 图表页 */
    .chart-wrap { margin-top: 16px; flex: 1; display: flex; flex-direction: column; gap: 10px; }
    .chart-row { display: flex; align-items: center; gap: 10px; }
    .chart-label {
      width: 120px; text-align: right;
      font-size: clamp(10px, 1.6vw, 15px);
      color: ${t.bodyColor}; flex-shrink: 0;
    }
    .chart-bar-wrap { flex: 1; background: ${t.borderColor}; border-radius: 3px; height: 28px; }
    .chart-bar {
      height: 100%; border-radius: 3px;
      background: ${t.accentColor};
      display: flex; align-items: center; padding-left: 8px;
      transition: width 0.6s ease;
    }
    .chart-val {
      font-size: clamp(10px, 1.6vw, 14px);
      color: #fff; font-weight: bold; white-space: nowrap;
    }

    /* 结尾页 */
    .slide.closing { text-align: center; align-items: center; }
    .closing-title {
      font-size: clamp(24px, 4vw, 48px);
      font-weight: bold; color: ${t.titleColor}; margin-bottom: 12px;
    }
    .closing-sub {
      font-size: clamp(12px, 2vw, 20px);
      color: ${t.mutedColor}; margin-bottom: 6px;
    }

    /* 导航 */
    #nav {
      display: flex; align-items: center; gap: 16px;
      margin-top: 16px; color: ${t.mutedColor};
      font-size: 14px;
    }
    .nav-btn {
      background: ${t.accentColor}; color: #fff;
      border: none; border-radius: 4px;
      padding: 8px 20px; cursor: pointer; font-size: 14px;
      opacity: 0.85;
    }
    .nav-btn:hover { opacity: 1; }
    .nav-btn:disabled { opacity: 0.3; cursor: default; }
    #counter { min-width: 60px; text-align: center; }
  `;

  function renderSlide(slide, idx) {
    const activeClass = idx === 0 ? ' active' : '';
    if (slide.type === 'cover') {
      return `<div class="slide cover${activeClass}" data-index="${idx}">
  <div class="cover-title">${slide.title}</div>
  <div class="accent-bar"></div>
  ${slide.subtitle ? `<div class="cover-subtitle">${slide.subtitle}</div>` : ''}
  ${slide.meta ? `<div class="cover-meta">${slide.meta}</div>` : ''}
</div>`;
    }
    if (slide.type === 'content') {
      const items = (slide.bullets || []).map(b => {
        if (typeof b === 'string') return `<li>${b}</li>`;
        return `<li class="sub">${b.sub}</li>`;
      }).join('\n    ');
      return `<div class="slide${activeClass}" data-index="${idx}">
  <div class="slide-title">${slide.title}</div>
  <ul class="bullet-list">
    ${items}
  </ul>
</div>`;
    }
    if (slide.type === 'two-column') {
      const leftItems = (slide.left.items || []).map(i => `<li>${i}</li>`).join('');
      const rightItems = (slide.right.items || []).map(i => `<li>${i}</li>`).join('');
      return `<div class="slide${activeClass}" data-index="${idx}">
  <div class="slide-title">${slide.title}</div>
  <div class="two-col">
    <div class="col">
      <div class="col-title">${slide.left.title}</div>
      <ul>${leftItems}</ul>
    </div>
    <div class="col">
      <div class="col-title">${slide.right.title}</div>
      <ul>${rightItems}</ul>
    </div>
  </div>
</div>`;
    }
    if (slide.type === 'chart') {
      const bars = (slide.data || []).map(d => {
        const pct = Math.min(100, Math.max(0, d.value));
        return `<div class="chart-row">
    <div class="chart-label">${d.label}</div>
    <div class="chart-bar-wrap">
      <div class="chart-bar" style="width:${pct}%">
        <span class="chart-val">${d.display || d.value + '%'}</span>
      </div>
    </div>
  </div>`;
      }).join('\n  ');
      return `<div class="slide${activeClass}" data-index="${idx}">
  <div class="slide-title">${slide.title}</div>
  <div class="chart-wrap">
  ${bars}
  </div>
</div>`;
    }
    if (slide.type === 'closing') {
      return `<div class="slide closing${activeClass}" data-index="${idx}">
  <div class="closing-title">${slide.title}</div>
  ${(slide.lines || []).map(l => `<div class="closing-sub">${l}</div>`).join('\n  ')}
</div>`;
    }
    return '';
  }

  const slidesHTML = slides.map((s, i) => renderSlide(s, i)).join('\n');
  const total = slides.length;

  const navJS = `
    var current = 0;
    var total = ${total};
    function show(n) {
      document.querySelectorAll('.slide').forEach(function(s, i) {
        s.classList.toggle('active', i === n);
      });
      document.getElementById('counter').textContent = (n + 1) + ' / ' + total;
      document.getElementById('prev').disabled = n === 0;
      document.getElementById('next').disabled = n === total - 1;
    }
    document.getElementById('prev').addEventListener('click', function() {
      if (current > 0) { current--; show(current); }
    });
    document.getElementById('next').addEventListener('click', function() {
      if (current < total - 1) { current++; show(current); }
    });
    document.addEventListener('keydown', function(e) {
      if (e.key === 'ArrowRight' || e.key === 'ArrowDown') {
        if (current < total - 1) { current++; show(current); }
      } else if (e.key === 'ArrowLeft' || e.key === 'ArrowUp') {
        if (current > 0) { current--; show(current); }
      }
    });
    show(0);
  `;

  return `<!DOCTYPE html>
<html lang="zh">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>${title}</title>
<style>
${css}
</style>
</head>
<body>
<div id="deck">
${slidesHTML}
</div>
<div id="nav">
  <button class="nav-btn" id="prev" disabled>&#8592; 上一页</button>
  <span id="counter">1 / ${total}</span>
  <button class="nav-btn" id="next">下一页 &#8594;</button>
</div>
<script>
${navJS}
</script>
</body>
</html>`;
}
```

---

## 示例：创建5页混合类型演示文稿

```javascript
// 将上方 buildHTMLSlides 函数复制到此处，然后执行：

const html = buildHTMLSlides({
  title: '2026年产品战略演示',
  theme: 'corporate',
  slides: [
    {
      type: 'cover',
      title: '2026年产品战略',
      subtitle: '用技术创造用户价值',
      meta: '产品团队 · 2026年5月3日'
    },
    {
      type: 'content',
      title: '核心战略方向',
      bullets: [
        '以用户需求为中心，深化产品体验',
        { sub: '通过数据分析洞察用户真实痛点' },
        { sub: '建立快速迭代的产品反馈闭环' },
        '扩大 AI 能力在核心功能中的覆盖',
        { sub: '智能推荐系统升级，准确率目标 85%+' },
        '国际化布局，重点进入东南亚市场',
        { sub: '本地化运营团队组建，Q3 完成' }
      ]
    },
    {
      type: 'two-column',
      title: '2025年回顾 vs 2026年目标',
      left: {
        title: '2025年成果',
        items: [
          '月活用户 280万',
          '年营收增长 32%',
          'NPS 评分 62',
          '新功能上线 18 项',
          '崩溃率降至 0.3%'
        ]
      },
      right: {
        title: '2026年目标',
        items: [
          '月活用户 500万',
          '年营收增长 45%',
          'NPS 评分 72+',
          '新功能上线 30 项',
          '崩溃率降至 0.1%'
        ]
      }
    },
    {
      type: 'chart',
      title: '各业务线营收占比（2026年Q1）',
      data: [
        { label: '订阅服务', value: 85, display: '42%（$18M）' },
        { label: '企业授权', value: 65, display: '32%（$14M）' },
        { label: '增值服务', value: 40, display: '20%（$8.5M）' },
        { label: '广告收入', value: 12, display: '6%（$2.6M）' }
      ]
    },
    {
      type: 'closing',
      title: '谢谢',
      lines: [
        '产品团队',
        'product@company.com',
        '欢迎提问与交流'
      ]
    }
  ]
});

console.log(html);
```

---

## 深色主题示例

```javascript
const html = buildHTMLSlides({
  title: '技术架构分享',
  theme: 'dark',
  slides: [
    {
      type: 'cover',
      title: '下一代技术架构',
      subtitle: '高可用 · 高性能 · 可扩展',
      meta: '技术部 · 架构组'
    },
    {
      type: 'content',
      title: '架构升级目标',
      bullets: [
        '服务响应时间 P99 < 100ms',
        '系统可用性 99.99%（年停机 < 52分钟）',
        '支持每秒 10万+ 并发请求',
        '微服务架构，独立部署与扩容',
        { sub: '服务拆分为 32 个独立模块' },
        { sub: 'Kubernetes 容器化编排' }
      ]
    },
    {
      type: 'chart',
      title: '性能优化结果对比',
      data: [
        { label: '接口延迟', value: 90, display: '优化 64%（320ms→115ms）' },
        { label: '吞吐量', value: 75, display: '提升 3.2x' },
        { label: '资源利用率', value: 60, display: '提升 45%' },
        { label: '部署频率', value: 80, display: '从每月到每日' }
      ]
    },
    {
      type: 'closing',
      title: '持续演进，永不停止',
      lines: ['技术驱动产品，产品创造价值']
    }
  ]
});

console.log(html);
```

---

## 使用说明

1. **运行代码** — 在 RikkaHub 中执行 JavaScript，`console.log()` 输出完整 HTML
2. **复制输出** — 复制全部输出内容（从 `<!DOCTYPE html>` 到 `</html>`）
3. **保存文件** — 新建文本文件，粘贴内容，保存为 `演示文稿名.html`
4. **浏览器全屏查看** — 用手机或电脑浏览器打开 `.html` 文件，按 F11 全屏
5. **翻页操作** — 键盘方向键（← →）或点击页面底部按钮翻页
6. **导出 PDF** — 浏览器中按 Ctrl+P（或「打印」）→ 目标选「另存为 PDF」→ 布局选「横向」

> 生成的 HTML 文件完全自包含，无需网络，无需安装任何软件，可直接分享给他人用浏览器查看。
