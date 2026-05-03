---
name: word-document-processing
description: "在手机端使用纯 JavaScript 生成 Word 兼容的 HTML 文档，可直接用 Word 打开，适用于 RikkaHub QuickJS 环境。当用户说「Word处理」「创建Word」「生成文档」「写报告」时触发。"
---

# Word 文档处理（QuickJS 手机版）

## 环境说明

RikkaHub 使用 **QuickJS** 引擎执行 JavaScript，**无法使用 `require()`、npm 包、`fs` 模块或任何 Node.js API**。

**解决方案：生成 Word 兼容的 HTML 文件。**

- QuickJS 生成完整 HTML 字符串，通过 `console.log()` 输出
- 用户复制输出内容，保存为 `.html` 文件
- 用 Microsoft Word 打开 `.html` 文件（Word 原生支持 HTML 导入）
- 在 Word 中选择「另存为」→ `.docx` 即可转换为标准 Word 文档

**流程：** `QuickJS 输出 HTML` → `保存为 .html` → `Word 打开` → `另存为 .docx`

---

## 页面规格

| 纸张 | 宽度 | 高度 | 页边距（推荐）|
|------|------|------|--------------|
| A4（推荐） | 210mm | 297mm | 上下左右各 25.4mm（1 英寸）|
| US Letter | 216mm | 279mm | 上下左右各 25.4mm（1 英寸）|

**横向：** 交换宽高值即可（A4 横向：297mm × 210mm）

---

## 核心函数：buildWordHTML()

```javascript
function buildWordHTML(config) {
  const {
    title = '文档标题',
    author = '',
    paperWidth = '210mm',
    paperHeight = '297mm',
    marginTop = '25.4mm',
    marginBottom = '25.4mm',
    marginLeft = '25.4mm',
    marginRight = '25.4mm',
    fontSize = '12pt',
    fontFamily = 'Arial, "Microsoft YaHei", sans-serif',
    lineHeight = '1.6',
    sections = []
  } = config;

  const metaXml = author ? `<meta name="author" content="${author}">` : '';

  const css = `
    @page {
      size: ${paperWidth} ${paperHeight};
      margin: ${marginTop} ${marginRight} ${marginBottom} ${marginLeft};
    }
    body {
      font-family: ${fontFamily};
      font-size: ${fontSize};
      line-height: ${lineHeight};
      color: #000000;
      margin: 0;
      padding: 0;
    }
    .page {
      width: ${paperWidth};
      min-height: ${paperHeight};
      margin: 0 auto;
      padding: ${marginTop} ${marginRight} ${marginBottom} ${marginLeft};
      box-sizing: border-box;
    }
    h1 {
      font-size: 20pt;
      font-weight: bold;
      margin: 12pt 0 6pt 0;
      page-break-after: avoid;
    }
    h2 {
      font-size: 16pt;
      font-weight: bold;
      margin: 10pt 0 5pt 0;
      page-break-after: avoid;
    }
    h3 {
      font-size: 13pt;
      font-weight: bold;
      margin: 8pt 0 4pt 0;
      page-break-after: avoid;
    }
    p {
      margin: 0 0 8pt 0;
      text-align: justify;
    }
    .cover {
      text-align: center;
      padding-top: 80mm;
    }
    .cover h1 {
      font-size: 28pt;
      margin-bottom: 10pt;
    }
    .cover .subtitle {
      font-size: 16pt;
      color: #555555;
      margin-bottom: 8pt;
    }
    .cover .meta {
      font-size: 11pt;
      color: #777777;
      margin-top: 20mm;
    }
    .page-break {
      page-break-after: always;
      height: 0;
      margin: 0;
      padding: 0;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin: 8pt 0;
      font-size: ${fontSize};
    }
    th, td {
      border: 1px solid #CCCCCC;
      padding: 5pt 8pt;
      text-align: left;
      vertical-align: top;
    }
    th {
      background-color: #D5E8F0;
      font-weight: bold;
    }
    tr:nth-child(even) td {
      background-color: #F5F5F5;
    }
    ul, ol {
      margin: 0 0 8pt 0;
      padding-left: 20pt;
    }
    li {
      margin-bottom: 4pt;
    }
    .indent {
      margin-left: 20pt;
    }
    strong { font-weight: bold; }
    em { font-style: italic; }
    .text-center { text-align: center; }
    .text-right { text-align: right; }
    .highlight { background-color: #FFFF99; }
  `;

  const bodyContent = sections.map(s => s).join('\n');

  return `<!DOCTYPE html>
<html xmlns:o="urn:schemas-microsoft-com:office:office"
      xmlns:w="urn:schemas-microsoft-com:office:word"
      xmlns="http://www.w3.org/TR/REC-html40">
<head>
<meta charset="UTF-8">
<meta name="ProgId" content="Word.Document">
<meta name="Generator" content="Microsoft Word">
${metaXml}
<title>${title}</title>
<!--[if gte mso 9]>
<xml>
  <w:WordDocument>
    <w:View>Print</w:View>
    <w:Zoom>100</w:Zoom>
    <w:DoNotOptimizeForBrowser/>
  </w:WordDocument>
</xml>
<![endif]-->
<style>
${css}
</style>
</head>
<body>
${bodyContent}
</body>
</html>`;
}

// 辅助函数：生成分页符
function pageBreak() {
  return '<div class="page-break"></div>';
}

// 辅助函数：生成表格
function buildTable(headers, rows) {
  const headerCells = headers.map(h => `<th>${h}</th>`).join('');
  const bodyRows = rows.map(row => {
    const cells = row.map(cell => `<td>${cell}</td>`).join('');
    return `<tr>${cells}</tr>`;
  }).join('\n    ');
  return `<table>
  <thead><tr>${headerCells}</tr></thead>
  <tbody>
    ${bodyRows}
  </tbody>
</table>`;
}

// 辅助函数：生成无序列表
function buildList(items) {
  const listItems = items.map(item => `<li>${item}</li>`).join('\n');
  return `<ul>\n${listItems}\n</ul>`;
}

// 辅助函数：生成有序列表
function buildOrderedList(items) {
  const listItems = items.map(item => `<li>${item}</li>`).join('\n');
  return `<ol>\n${listItems}\n</ol>`;
}
```

---

## 示例一：创建带封面页的正式报告

```javascript
function buildWordHTML(config) {
  const {
    title = '文档标题', author = '', paperWidth = '210mm', paperHeight = '297mm',
    marginTop = '25.4mm', marginBottom = '25.4mm', marginLeft = '25.4mm', marginRight = '25.4mm',
    fontSize = '12pt', fontFamily = 'Arial, "Microsoft YaHei", sans-serif',
    lineHeight = '1.6', sections = []
  } = config;
  const metaXml = author ? `<meta name="author" content="${author}">` : '';
  const css = `@page{size:${paperWidth} ${paperHeight};margin:${marginTop} ${marginRight} ${marginBottom} ${marginLeft};}body{font-family:${fontFamily};font-size:${fontSize};line-height:${lineHeight};color:#000;margin:0;padding:0;}h1{font-size:20pt;font-weight:bold;margin:12pt 0 6pt 0;}h2{font-size:16pt;font-weight:bold;margin:10pt 0 5pt 0;}h3{font-size:13pt;font-weight:bold;margin:8pt 0 4pt 0;}p{margin:0 0 8pt 0;text-align:justify;}.cover{text-align:center;padding-top:80mm;}.cover h1{font-size:28pt;margin-bottom:10pt;}.cover .subtitle{font-size:16pt;color:#555;margin-bottom:8pt;}.cover .meta{font-size:11pt;color:#777;margin-top:20mm;}.page-break{page-break-after:always;height:0;}table{width:100%;border-collapse:collapse;margin:8pt 0;}th,td{border:1px solid #CCC;padding:5pt 8pt;text-align:left;}th{background-color:#D5E8F0;font-weight:bold;}ul,ol{margin:0 0 8pt 0;padding-left:20pt;}li{margin-bottom:4pt;}`;
  const bodyContent = sections.join('\n');
  return `<!DOCTYPE html>\n<html xmlns:o="urn:schemas-microsoft-com:office:office" xmlns:w="urn:schemas-microsoft-com:office:word" xmlns="http://www.w3.org/TR/REC-html40">\n<head>\n<meta charset="UTF-8">\n<meta name="ProgId" content="Word.Document">\n${metaXml}\n<title>${title}</title>\n<style>${css}</style>\n</head>\n<body>\n${bodyContent}\n</body>\n</html>`;
}

// 封面页
const coverPage = `
<div class="cover">
  <h1>2026年度业务发展报告</h1>
  <p class="subtitle">战略规划与执行分析</p>
  <p class="subtitle">技术部门</p>
  <p class="meta">编制日期：2026年5月3日<br>版本：V1.0<br>保密等级：内部</p>
</div>`;

// 分页符
const br = '<div class="page-break"></div>';

// 摘要页
const summaryPage = `
<h1>执行摘要</h1>
<p>本报告全面回顾2026年度业务发展情况，分析主要市场趋势，并提出下一阶段的战略建议。</p>
<p>核心发现如下：</p>
<ul>
  <li>年度营收同比增长 <strong>32%</strong>，超过预期目标 8 个百分点</li>
  <li>用户规模突破 <strong>500万</strong>，月活跃用户达到 280万</li>
  <li>新产品线贡献收入占比提升至 <strong>45%</strong></li>
  <li>运营成本优化，利润率提升至 <strong>23%</strong></li>
</ul>`;

// 正文章节
const chapter1 = `
<h1>第一章 市场环境分析</h1>
<h2>1.1 行业背景</h2>
<p>2026年全球科技市场持续快速增长，AI 技术的广泛应用推动了各行业的数字化转型进程。</p>
<h2>1.2 竞争态势</h2>
<p>市场竞争格局逐渐清晰，头部企业优势持续扩大，中小企业通过差异化策略寻求突破。</p>
<h3>1.2.1 主要竞争对手分析</h3>
<p>通过对主要竞争对手的跟踪研究，我们识别出以下关键趋势：</p>
<ol>
  <li>产品功能持续迭代，用户体验成为核心竞争力</li>
  <li>数据安全与隐私保护受到更高重视</li>
  <li>本地化运营能力成为市场份额的决定因素</li>
</ol>`;

const html = buildWordHTML({
  title: '2026年度业务发展报告',
  author: '技术部门',
  sections: [coverPage, br, summaryPage, br, chapter1]
});

console.log(html);
```

---

## 示例二：创建带表格的业务文档

```javascript
function buildWordHTML(config) {
  const {
    title = '文档标题', sections = [],
    fontFamily = 'Arial, "Microsoft YaHei", sans-serif', fontSize = '12pt', lineHeight = '1.6'
  } = config;
  const css = `body{font-family:${fontFamily};font-size:${fontSize};line-height:${lineHeight};color:#000;margin:25.4mm;}h1{font-size:20pt;font-weight:bold;margin:12pt 0 6pt 0;}h2{font-size:16pt;font-weight:bold;margin:10pt 0 5pt 0;}p{margin:0 0 8pt 0;}table{width:100%;border-collapse:collapse;margin:8pt 0;}th,td{border:1px solid #CCC;padding:5pt 8pt;text-align:left;vertical-align:top;}th{background-color:#D5E8F0;font-weight:bold;}tr:nth-child(even) td{background-color:#F5F5F5;}.page-break{page-break-after:always;height:0;}`;
  return `<!DOCTYPE html>\n<html xmlns:o="urn:schemas-microsoft-com:office:office" xmlns:w="urn:schemas-microsoft-com:office:word" xmlns="http://www.w3.org/TR/REC-html40">\n<head><meta charset="UTF-8"><meta name="ProgId" content="Word.Document"><title>${title}</title><style>${css}</style></head>\n<body>\n${sections.join('\n')}\n</body></html>`;
}

function buildTable(headers, rows) {
  const hCells = headers.map(h => `<th>${h}</th>`).join('');
  const bRows = rows.map(r => `<tr>${r.map(c => `<td>${c}</td>`).join('')}</tr>`).join('\n');
  return `<table><thead><tr>${hCells}</tr></thead><tbody>${bRows}</tbody></table>`;
}

const projectTable = buildTable(
  ['项目名称', '负责人', '状态', '完成度', '截止日期'],
  [
    ['用户体验优化', '张三', '进行中', '75%', '2026-06-30'],
    ['数据分析平台', '李四', '已完成', '100%', '2026-04-15'],
    ['移动端重构', '王五', '计划中', '0%', '2026-09-01'],
    ['API 接口升级', '赵六', '进行中', '50%', '2026-07-31']
  ]
);

const budgetTable = buildTable(
  ['费用类别', '预算（万元）', '实际（万元）', '差异', '备注'],
  [
    ['人力成本', '120', '115', '-5', '低于预算'],
    ['技术采购', '45', '52', '+7', '新增服务器'],
    ['市场推广', '30', '28', '-2', '活动调整'],
    ['运营费用', '25', '24', '-1', '正常'],
    ['<strong>合计</strong>', '<strong>220</strong>', '<strong>219</strong>', '<strong>-1</strong>', '']
  ]
);

const content = `
<h1>项目进展与预算报告</h1>
<p>截至 2026年5月，各项目整体推进顺利，预算执行情况良好。</p>

<h2>一、项目状态汇总</h2>
${projectTable}

<h2>二、预算执行情况</h2>
${budgetTable}

<h2>三、下阶段工作计划</h2>
<p>重点推进移动端重构项目的需求评审，同时持续跟进 API 升级进度，确保在截止日期前完成交付。</p>`;

const html = buildWordHTML({
  title: '项目进展与预算报告',
  sections: [content]
});

console.log(html);
```

---

## 示例三：创建多章节文档（含 h1/h2/h3 层级）

```javascript
function buildWordHTML(config) {
  const {
    title = '文档标题', sections = [],
    fontFamily = 'Arial, "Microsoft YaHei", sans-serif', fontSize = '12pt'
  } = config;
  const css = `body{font-family:${fontFamily};font-size:${fontSize};line-height:1.6;color:#000;margin:25.4mm;}h1{font-size:20pt;font-weight:bold;color:#1a3a6b;margin:16pt 0 6pt 0;border-bottom:2px solid #1a3a6b;padding-bottom:4pt;}h2{font-size:15pt;font-weight:bold;color:#1a3a6b;margin:12pt 0 5pt 0;}h3{font-size:12pt;font-weight:bold;color:#333;margin:8pt 0 4pt 0;}p{margin:0 0 8pt 0;text-align:justify;}.page-break{page-break-after:always;height:0;}ul,ol{margin:0 0 8pt 0;padding-left:20pt;}li{margin-bottom:3pt;}`;
  return `<!DOCTYPE html>\n<html xmlns:o="urn:schemas-microsoft-com:office:office" xmlns:w="urn:schemas-microsoft-com:office:word" xmlns="http://www.w3.org/TR/REC-html40">\n<head><meta charset="UTF-8"><meta name="ProgId" content="Word.Document"><title>${title}</title><style>${css}</style></head>\n<body>${sections.join('\n')}</body></html>`;
}

const chapters = [
  {
    title: '第一章 引言',
    sections: [
      { title: '1.1 研究背景', content: '随着人工智能技术的快速发展，自然语言处理在各领域的应用日益广泛。本研究旨在探索大语言模型在企业场景中的实际应用价值。' },
      { title: '1.2 研究目标', subsections: [
        { title: '1.2.1 核心目标', content: '评估大语言模型在提升工作效率方面的实际效果，量化其对业务流程的影响。' },
        { title: '1.2.2 次要目标', content: '识别应用过程中的主要障碍和解决方案，为企业规模化落地提供参考。' }
      ]}
    ]
  },
  {
    title: '第二章 研究方法',
    sections: [
      { title: '2.1 数据收集', content: '通过问卷调查、访谈及系统日志分析三种方式收集数据，样本覆盖 12 家不同规模的企业，共 350 名参与者。' },
      { title: '2.2 分析框架', content: '采用混合研究方法，结合定量统计分析与定性案例研究，确保结论的信效度。' }
    ]
  },
  {
    title: '第三章 研究结论',
    sections: [
      { title: '3.1 主要发现', content: '研究表明，合理应用大语言模型可将文档处理效率提升 40-60%，客户响应速度提升 35%。' },
      { title: '3.2 建议', content: '建议企业从低风险场景入手，逐步扩大应用范围，同时建立完善的质量控制机制。' }
    ]
  }
];

function renderChapters(chapters) {
  return chapters.map((ch, idx) => {
    const sectionHTML = ch.sections.map(sec => {
      let html = `<h2>${sec.title}</h2>`;
      if (sec.content) html += `<p>${sec.content}</p>`;
      if (sec.subsections) {
        html += sec.subsections.map(sub =>
          `<h3>${sub.title}</h3><p>${sub.content}</p>`
        ).join('');
      }
      return html;
    }).join('');
    const br = idx < chapters.length - 1 ? '<div class="page-break"></div>' : '';
    return `<h1>${ch.title}</h1>${sectionHTML}${br}`;
  }).join('');
}

const html = buildWordHTML({
  title: '大语言模型企业应用研究报告',
  sections: [renderChapters(chapters)]
});

console.log(html);
```

---

## 使用说明

1. **运行代码** — 在 RikkaHub 中执行 JavaScript，`console.log()` 输出完整 HTML
2. **复制输出** — 复制全部输出内容
3. **保存文件** — 新建文本文件，粘贴内容，保存为 `文档名.html`
4. **用 Word 打开** — 双击 `.html` 文件，或在 Word 中选择「打开」
5. **另存为 .docx** — 在 Word 中选择「文件」→「另存为」→ 格式选「Word 文档（.docx）」

> Word 完全支持带命名空间的 HTML（`xmlns:w` 等），打开后会自动识别样式并转换为 Word 格式。
