---
name: pdf-file-processing
description: "在手机端使用纯 JavaScript 生成可打印的 HTML 文档，保存后可直接打印为 PDF，适用于 RikkaHub QuickJS 环境。当用户说「PDF生成」「生成PDF」「创建报告」「打印文档」时触发。"
---

# PDF 文档生成（手机版，QuickJS）

## 环境说明

RikkaHub 使用 **QuickJS** 引擎执行 JavaScript，有以下限制：

- **不支持** `require()`、npm 包、`fs` 模块、Node.js API
- **只支持** 纯 ES2020+ JavaScript
- 输出通过 `console.log()` 返回 HTML 文本

**工作流程：**
1. 运行脚本 → 输出完整 HTML 字符串
2. 复制输出内容 → 保存为 `.html` 文件
3. 浏览器打开 → 使用打印功能 → 另存为 PDF

---

## 核心函数：buildPrintHTML()

```javascript
function buildPrintHTML({ title, author, date, sections, styles = '' }) {
  const defaultStyles = `
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body {
      font-family: 'Arial', 'Helvetica', sans-serif;
      font-size: 12pt;
      color: #000;
      background: #fff;
    }
    .page {
      width: 210mm;
      min-height: 297mm;
      padding: 20mm;
      margin: 0 auto;
      background: #fff;
    }
    h1 { font-size: 24pt; margin-bottom: 8pt; color: #1a1a2e; }
    h2 { font-size: 16pt; margin-bottom: 6pt; margin-top: 16pt; color: #16213e; border-bottom: 1px solid #ccc; padding-bottom: 4pt; }
    h3 { font-size: 13pt; margin-bottom: 4pt; margin-top: 12pt; color: #0f3460; }
    p { line-height: 1.6; margin-bottom: 8pt; }
    table { width: 100%; border-collapse: collapse; margin: 8pt 0; font-size: 11pt; }
    th { background: #1a1a2e; color: #fff; padding: 6pt 8pt; text-align: left; }
    td { padding: 5pt 8pt; border-bottom: 1px solid #ddd; }
    tr:nth-child(even) td { background: #f5f5f5; }
    .header-line { border-top: 2px solid #1a1a2e; margin: 6pt 0 16pt; }
    .footer { position: fixed; bottom: 10mm; left: 20mm; right: 20mm; text-align: center; font-size: 9pt; color: #888; border-top: 1px solid #ddd; padding-top: 4pt; }
    .page-break { page-break-after: always; }
    .toc { margin: 16pt 0; padding: 12pt; background: #f9f9f9; border-left: 3px solid #1a1a2e; }
    .toc-item { padding: 3pt 0; }
    .highlight { background: #fff9c4; padding: 2pt 4pt; }
    @page { size: A4; margin: 20mm; }
    @media print {
      body { background: #fff; }
      .page { width: 100%; padding: 0; margin: 0; }
      .no-print { display: none; }
    }
  `;

  const metaInfo = `
    <div style="margin-bottom:4pt; font-size:10pt; color:#555;">
      ${author ? `作者：${author}` : ''}
      ${author && date ? ' &nbsp;|&nbsp; ' : ''}
      ${date ? `日期：${date}` : ''}
    </div>
  `;

  const bodyContent = sections.map(s => s).join('\n');

  return `<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>${title}</title>
  <style>
${defaultStyles}
${styles}
  </style>
</head>
<body>
  <div class="page">
    <h1>${title}</h1>
    ${metaInfo}
    <div class="header-line"></div>
    ${bodyContent}
  </div>
  <div class="footer">${title} &nbsp;|&nbsp; ${date || ''}</div>
</body>
</html>`;
}
```

---

## A4 页面规格

| 属性 | 数值 |
|------|------|
| 纸张尺寸 | 210mm × 297mm |
| 页边距 | 上下左右各 20mm |
| 内容区域宽度 | 170mm |
| 内容区域高度 | 257mm |
| CSS `@page` 设置 | `size: A4; margin: 20mm;` |

---

## 示例：生成单页报告

```javascript
function buildPrintHTML({ title, author, date, sections, styles = '' }) {
  const defaultStyles = `
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body { font-family: Arial, Helvetica, sans-serif; font-size: 12pt; color: #000; }
    .page { width: 210mm; min-height: 297mm; padding: 20mm; margin: 0 auto; background: #fff; }
    h1 { font-size: 24pt; margin-bottom: 8pt; color: #1a1a2e; }
    h2 { font-size: 16pt; margin-bottom: 6pt; margin-top: 16pt; color: #16213e; border-bottom: 1px solid #ccc; padding-bottom: 4pt; }
    p { line-height: 1.6; margin-bottom: 8pt; }
    .header-line { border-top: 2px solid #1a1a2e; margin: 6pt 0 16pt; }
    .footer { position: fixed; bottom: 10mm; left: 20mm; right: 20mm; text-align: center; font-size: 9pt; color: #888; border-top: 1px solid #ddd; padding-top: 4pt; }
    @page { size: A4; margin: 20mm; }
    @media print { body { background: #fff; } .page { width: 100%; padding: 0; margin: 0; } }
  `;
  const metaInfo = `<div style="margin-bottom:4pt;font-size:10pt;color:#555;">${author ? '作者：' + author : ''}${author && date ? ' | ' : ''}${date ? '日期：' + date : ''}</div>`;
  const bodyContent = sections.join('\n');
  return `<!DOCTYPE html><html lang="zh-CN"><head><meta charset="UTF-8"><title>${title}</title><style>${defaultStyles}</style></head><body><div class="page"><h1>${title}</h1>${metaInfo}<div class="header-line"></div>${bodyContent}</div><div class="footer">${title} | ${date || ''}</div></body></html>`;
}

const html = buildPrintHTML({
  title: '季度业务报告',
  author: '战略分析部',
  date: '2025年Q1',
  sections: [
    `<h2>执行摘要</h2>
     <p>本季度公司整体运营保持稳健增长态势，核心业务指标均达到或超过年度目标。收入同比增长 18.5%，EBITDA 利润率提升至 32%。</p>`,

    `<h2>关键指标</h2>
     <p><strong>收入：</strong>¥2,850万（同比 +18.5%）</p>
     <p><strong>毛利润：</strong>¥1,710万（毛利率 60%）</p>
     <p><strong>净利润：</strong>¥570万（净利率 20%）</p>
     <p><strong>新增客户：</strong>142 家</p>`,

    `<h2>下季度展望</h2>
     <p>预计 Q2 收入达到 ¥3,200万，重点推进海外市场拓展和新产品发布计划。</p>`,
  ]
});

console.log(html);
```

---

## 示例：生成多页带目录的文档

```javascript
function escapeHtml(str) {
  return String(str)
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;');
}

function buildMultiPageHTML({ title, author, date, chapters }) {
  const styles = `
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body { font-family: Arial, Helvetica, sans-serif; font-size: 12pt; color: #000; background: #fff; }
    .page { width: 210mm; padding: 20mm; margin: 0 auto; background: #fff; }
    h1 { font-size: 26pt; margin-bottom: 8pt; color: #1a1a2e; }
    h2 { font-size: 18pt; margin-top: 24pt; margin-bottom: 8pt; color: #16213e; border-bottom: 2px solid #1a1a2e; padding-bottom: 4pt; }
    h3 { font-size: 13pt; margin-top: 14pt; margin-bottom: 6pt; color: #0f3460; }
    p { line-height: 1.7; margin-bottom: 10pt; }
    .header-line { border-top: 2px solid #1a1a2e; margin: 8pt 0 20pt; }
    .toc { padding: 16pt; background: #f4f6fa; border-left: 4px solid #1a1a2e; margin-bottom: 20pt; }
    .toc h2 { border: none; margin-top: 0; }
    .toc-item { padding: 4pt 0; display: flex; justify-content: space-between; border-bottom: 1px dotted #bbb; }
    .page-break { page-break-after: always; height: 0; }
    .chapter-header { background: #1a1a2e; color: #fff; padding: 10pt 16pt; margin-bottom: 16pt; border-radius: 2pt; }
    .footer { text-align: center; font-size: 9pt; color: #888; margin-top: 40pt; border-top: 1px solid #ddd; padding-top: 6pt; }
    @page { size: A4; margin: 20mm; }
    @media print { .page { width: 100%; padding: 0; margin: 0; } }
  `;

  // 目录
  const tocItems = chapters.map((ch, i) =>
    `<div class="toc-item"><span>${i + 1}. ${escapeHtml(ch.title)}</span><span>第 ${i + 2} 页</span></div>`
  ).join('');

  const tocSection = `
    <div class="toc">
      <h2>目录</h2>
      ${tocItems}
    </div>
    <div class="page-break"></div>
  `;

  // 章节页
  const chapterPages = chapters.map((ch, i) => `
    <div class="chapter-header">第 ${i + 1} 章：${escapeHtml(ch.title)}</div>
    ${ch.content}
    <div class="footer">${escapeHtml(title)} — 第 ${i + 2} 页 / 共 ${chapters.length + 1} 页</div>
    ${i < chapters.length - 1 ? '<div class="page-break"></div>' : ''}
  `).join('');

  return `<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>${escapeHtml(title)}</title>
  <style>${styles}</style>
</head>
<body>
  <div class="page">
    <h1>${escapeHtml(title)}</h1>
    <div style="font-size:10pt;color:#555;margin-bottom:6pt;">
      ${author ? '作者：' + escapeHtml(author) + ' &nbsp;|&nbsp; ' : ''}
      ${date ? '日期：' + escapeHtml(date) : ''}
    </div>
    <div class="header-line"></div>
    ${tocSection}
    ${chapterPages}
  </div>
</body>
</html>`;
}

const html = buildMultiPageHTML({
  title: '年度战略规划报告',
  author: '管理层',
  date: '2025年1月',
  chapters: [
    {
      title: '市场分析',
      content: `
        <h3>行业概况</h3>
        <p>目标市场规模约为 500 亿元，年均增速 12%，竞争格局以头部三家企业为主，合计占据 45% 市场份额。</p>
        <h3>竞争态势</h3>
        <p>主要竞争对手在产品功能和价格策略上保持稳定，本公司差异化优势在于服务响应速度和定制化能力。</p>
      `
    },
    {
      title: '战略目标',
      content: `
        <h3>三年目标</h3>
        <p>至 2027 年，实现收入规模突破 2 亿元，市场占有率提升至 8%，建立覆盖全国的服务网络。</p>
        <h3>关键举措</h3>
        <p>重点推进产品升级、渠道拓展和人才引进三大战略，计划新增核心技术人员 50 名。</p>
      `
    },
    {
      title: '执行计划',
      content: `
        <h3>Q1–Q2 重点任务</h3>
        <p>完成新产品发布，启动三个重点城市的市场推广活动，目标新增签约客户 200 家。</p>
        <h3>资源投入</h3>
        <p>计划全年研发投入 ¥3,000 万，市场推广预算 ¥1,500 万，运营支出同比控制在 10% 以内。</p>
      `
    }
  ]
});

console.log(html);
```

---

## 示例：生成带表格的财务报告

```javascript
function buildFinancialReportHTML({ title, company, date, data }) {
  const styles = `
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body { font-family: Arial, Helvetica, sans-serif; font-size: 11pt; color: #000; background: #fff; }
    .page { width: 210mm; padding: 20mm; margin: 0 auto; background: #fff; }
    h1 { font-size: 22pt; margin-bottom: 4pt; color: #1a1a2e; }
    h2 { font-size: 14pt; margin-top: 18pt; margin-bottom: 8pt; color: #16213e; border-left: 4px solid #1a1a2e; padding-left: 8pt; }
    .meta { font-size: 10pt; color: #555; margin-bottom: 12pt; }
    .header-line { border-top: 2px solid #1a1a2e; margin: 8pt 0 16pt; }
    table { width: 100%; border-collapse: collapse; margin: 8pt 0 16pt; font-size: 10.5pt; }
    th { background: #1a1a2e; color: #fff; padding: 7pt 10pt; text-align: right; font-weight: bold; }
    th:first-child { text-align: left; }
    td { padding: 6pt 10pt; border-bottom: 1px solid #e0e0e0; text-align: right; }
    td:first-child { text-align: left; font-weight: 500; }
    tr:nth-child(even) td { background: #f7f9fc; }
    .total-row td { font-weight: bold; border-top: 2px solid #1a1a2e; background: #eef2f8 !important; }
    .growth { color: #1a7a3c; }
    .decline { color: #c0392b; }
    .note { font-size: 9pt; color: #777; margin-top: 8pt; }
    .footer { margin-top: 30pt; text-align: center; font-size: 9pt; color: #aaa; border-top: 1px solid #ddd; padding-top: 6pt; }
    @page { size: A4; margin: 20mm; }
    @media print { .page { width: 100%; padding: 0; margin: 0; } }
  `;

  function fmtNum(n) {
    if (n === 0 || n === null || n === undefined) return '-';
    return Number(n).toLocaleString('zh-CN');
  }

  function fmtPct(n) {
    if (n === null || n === undefined) return '-';
    const cls = n >= 0 ? 'growth' : 'decline';
    const sign = n >= 0 ? '+' : '';
    return `<span class="${cls}">${sign}${n.toFixed(1)}%</span>`;
  }

  // 损益表
  const incomeRows = data.income.map(row =>
    `<tr${row.isTotal ? ' class="total-row"' : ''}>
      <td>${row.label}</td>
      ${row.years.map(y => `<td>${fmtNum(y)}</td>`).join('')}
      <td>${row.yoy !== undefined ? fmtPct(row.yoy) : '-'}</td>
    </tr>`
  ).join('');

  const yearHeaders = data.yearLabels.map(y => `<th>${y}</th>`).join('');

  const incomeTable = `
    <h2>损益表（单位：万元）</h2>
    <table>
      <thead><tr><th>项目</th>${yearHeaders}<th>同比</th></tr></thead>
      <tbody>${incomeRows}</tbody>
    </table>
    <p class="note">* 数字单位为人民币万元；同比为最新年度与上一年度比较。</p>
  `;

  return `<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>${title}</title>
  <style>${styles}</style>
</head>
<body>
  <div class="page">
    <h1>${title}</h1>
    <div class="meta">${company} &nbsp;|&nbsp; ${date}</div>
    <div class="header-line"></div>
    ${incomeTable}
    <div class="footer">本报告由系统自动生成 &nbsp;|&nbsp; ${company} &nbsp;|&nbsp; ${date}</div>
  </div>
</body>
</html>`;
}

const html = buildFinancialReportHTML({
  title: '2024年度财务报告',
  company: '示例科技有限公司',
  date: '2025年3月',
  data: {
    yearLabels: ['2022', '2023', '2024'],
    income: [
      { label: '营业收入',    years: [15200, 18500, 22800], yoy: 23.2 },
      { label: '营业成本',    years: [6840,  8140,  9804],  yoy: 20.4 },
      { label: '毛利润',      years: [8360,  10360, 12996], yoy: 25.4, isTotal: false },
      { label: '毛利率',      years: ['55.0%', '56.0%', '57.0%'], yoy: null },
      { label: '销售费用',    years: [1520,  1850,  2280],  yoy: 23.2 },
      { label: '管理费用',    years: [760,   925,   1140],  yoy: 23.2 },
      { label: '研发费用',    years: [1216,  1665,  2052],  yoy: 23.2 },
      { label: '营业利润',    years: [4864,  5920,  7524],  yoy: 27.1, isTotal: true },
      { label: '净利润',      years: [3648,  4440,  5643],  yoy: 27.1, isTotal: true },
    ]
  }
});

console.log(html);
```

---

## 打印说明

1. **运行脚本** → 在 RikkaHub 中执行，获得 HTML 文本输出
2. **复制 HTML 代码** → 选中全部输出内容并复制
3. **保存为 .html** → 粘贴到文本编辑器，保存文件名以 `.html` 结尾
4. **浏览器打开** → 用 Chrome / Safari / Edge 打开该 .html 文件
5. **打印/另存为 PDF** → 按 `Ctrl+P`（电脑）或浏览器菜单选择打印
   - 选择"另存为 PDF"或打印机选"PDF"
   - 纸张选 A4，启用"背景图形"以保留颜色
6. **获得 PDF** → 保存到设备或分享
