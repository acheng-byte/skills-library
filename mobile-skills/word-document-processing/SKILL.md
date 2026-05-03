---
name: word-document-processing
description: "在手机端使用 JavaScript（docx 包）创建和处理 Word 文档，适用于 RikkaHub 环境。当用户说「Word处理」「创建Word」「生成文档」「手机Word」时触发。"
---

# Word 文档处理（手机版）

## 环境说明

手机端（RikkaHub）可执行 **JavaScript**，创建 Word 文档使用 **docx** 包。

安装依赖：
```
npm install docx
```

---

## 快速参考

| 任务 | 方式 |
|------|------|
| 创建新文档 | 用 `docx` 包（见下方示例） |
| 读取文档内容 | 将 .docx 上传后让 AI 分析 |
| 编辑已有文档 | 读取内容 → AI 修改 → 重新生成 |

---

## 创建新文档

```javascript
const {
  Document, Packer, Paragraph, TextRun, Table, TableRow, TableCell,
  Header, Footer, AlignmentType, PageOrientation, LevelFormat,
  ExternalHyperlink, InternalHyperlink, Bookmark, FootnoteReferenceRun,
  PositionalTab, PositionalTabAlignment, PositionalTabRelativeTo,
  PositionalTabLeader, TabStopType, TabStopPosition, Column, SectionType,
  TableOfContents, HeadingLevel, BorderStyle, WidthType, ShadingType,
  VerticalAlign, PageNumber, PageBreak
} = require('docx');
const fs = require('fs');

const doc = new Document({
  sections: [{
    properties: {
      page: {
        size: {
          width: 11906,  // A4 宽（DXA）
          height: 16838  // A4 高（DXA）
        },
        margin: { top: 1440, right: 1440, bottom: 1440, left: 1440 } // 2.54cm 页边距
      }
    },
    children: [
      new Paragraph({
        heading: HeadingLevel.HEADING_1,
        children: [new TextRun('文档标题')]
      }),
      new Paragraph({
        children: [new TextRun('正文内容示例。')]
      })
    ]
  }]
});

Packer.toBuffer(doc).then(buffer => {
  fs.writeFileSync('output.docx', buffer);
  console.log('文档已生成: output.docx');
});
```

---

## 页面尺寸（DXA 单位，1440 DXA = 1 英寸）

| 纸张 | 宽 | 高 | 内容宽（2.54cm 页边距）|
|------|----|----|----------------------|
| A4（推荐） | 11,906 | 16,838 | 9,026 |
| US Letter | 12,240 | 15,840 | 9,360 |

**横向：**
```javascript
size: {
  width: 11906,
  height: 16838,
  orientation: PageOrientation.LANDSCAPE  // docx 会自动交换宽高
}
```

---

## 样式设置

```javascript
const doc = new Document({
  styles: {
    default: {
      document: { run: { font: 'Arial', size: 24 } }  // 默认 12pt
    },
    paragraphStyles: [
      {
        id: 'Heading1', name: 'Heading 1',
        basedOn: 'Normal', next: 'Normal', quickFormat: true,
        run: { size: 32, bold: true, font: 'Arial' },
        paragraph: { spacing: { before: 240, after: 240 }, outlineLevel: 0 }
      },
      {
        id: 'Heading2', name: 'Heading 2',
        basedOn: 'Normal', next: 'Normal', quickFormat: true,
        run: { size: 28, bold: true, font: 'Arial' },
        paragraph: { spacing: { before: 180, after: 180 }, outlineLevel: 1 }
      }
    ]
  },
  sections: [{ children: [] }]
});
```

---

## 列表

```javascript
// 不要直接用 • 符号，使用 numbering config
const doc = new Document({
  numbering: {
    config: [
      {
        reference: 'bullets',
        levels: [{
          level: 0, format: LevelFormat.BULLET, text: '•',
          alignment: AlignmentType.LEFT,
          style: { paragraph: { indent: { left: 720, hanging: 360 } } }
        }]
      },
      {
        reference: 'numbers',
        levels: [{
          level: 0, format: LevelFormat.DECIMAL, text: '%1.',
          alignment: AlignmentType.LEFT,
          style: { paragraph: { indent: { left: 720, hanging: 360 } } }
        }]
      }
    ]
  },
  sections: [{
    children: [
      new Paragraph({
        numbering: { reference: 'bullets', level: 0 },
        children: [new TextRun('无序列表项')]
      }),
      new Paragraph({
        numbering: { reference: 'numbers', level: 0 },
        children: [new TextRun('有序列表项')]
      })
    ]
  }]
});
```

---

## 表格

```javascript
// 关键：必须同时设置表格 columnWidths 和每个单元格的 width
const border = { style: BorderStyle.SINGLE, size: 1, color: 'CCCCCC' };
const borders = { top: border, bottom: border, left: border, right: border };

new Table({
  width: { size: 9026, type: WidthType.DXA },  // A4 内容宽
  columnWidths: [4513, 4513],  // 两列各占一半，合计等于表格宽
  rows: [
    new TableRow({
      children: [
        new TableCell({
          borders,
          width: { size: 4513, type: WidthType.DXA },
          shading: { fill: 'D5E8F0', type: ShadingType.CLEAR },
          margins: { top: 80, bottom: 80, left: 120, right: 120 },
          children: [new Paragraph({ children: [new TextRun('单元格内容')] })]
        })
      ]
    })
  ]
})
```

**宽度规则：**
- 始终用 `WidthType.DXA`，不用 `WidthType.PERCENTAGE`（Google Docs 不兼容）
- 表格宽 = 所有列宽之和
- 单元格 margin 是内边距，不影响列宽计算
- 底纹用 `ShadingType.CLEAR`，不用 SOLID（否则背景变黑）

---

## 超链接

```javascript
// 外部链接
new Paragraph({
  children: [new ExternalHyperlink({
    children: [new TextRun({ text: '点击这里', style: 'Hyperlink' })],
    link: 'https://example.com'
  })]
})

// 内部书签链接
new Paragraph({
  heading: HeadingLevel.HEADING_1,
  children: [new Bookmark({ id: 'chapter1', children: [new TextRun('第一章')] })]
})
new Paragraph({
  children: [new InternalHyperlink({
    children: [new TextRun({ text: '跳转到第一章', style: 'Hyperlink' })],
    anchor: 'chapter1'
  })]
})
```

---

## 页眉页脚

```javascript
sections: [{
  properties: {
    page: { margin: { top: 1440, right: 1440, bottom: 1440, left: 1440 } }
  },
  headers: {
    default: new Header({
      children: [new Paragraph({ children: [new TextRun('页眉文字')] })]
    })
  },
  footers: {
    default: new Footer({
      children: [new Paragraph({
        children: [
          new TextRun('第 '),
          new TextRun({ children: [PageNumber.CURRENT] }),
          new TextRun(' 页')
        ]
      })]
    })
  },
  children: []
}]
```

---

## 目录

```javascript
// 标题必须使用 HeadingLevel，不能用自定义样式
new TableOfContents('目录', { hyperlink: true, headingStyleRange: '1-3' })
```

---

## 分页

```javascript
// PageBreak 必须放在 Paragraph 里
new Paragraph({ children: [new PageBreak()] })
// 或
new Paragraph({ pageBreakBefore: true, children: [new TextRun('新页内容')] })
```

---

## 关键注意事项

- **不要用 `\n`** 换行 — 使用独立的 Paragraph 元素
- **不要直接用 unicode 符号（• 等）** — 使用 `LevelFormat.BULLET`
- **PageBreak 必须在 Paragraph 里** — 否则生成无效 XML
- **表格必须双设宽度** — `columnWidths` 数组 + 每个单元格的 `width`
- **底纹用 `ShadingType.CLEAR`** — 不用 SOLID
- **目录只能用 HeadingLevel** — 不支持自定义样式
- **样式 ID 要精确** — "Heading1"、"Heading2" 等

---

## 依赖

```
npm install docx
```
