---
name: pdf-file-processing
description: "在手机端使用 JavaScript（pdf-lib）处理 PDF 文件，适用于 RikkaHub 环境。当用户说「PDF处理」「操作PDF」「生成PDF」「手机PDF」时触发。"
---

# PDF 文件处理（手机版）

## 环境说明

手机端（RikkaHub）可执行 **JavaScript**，处理 PDF 使用 **pdf-lib** 包。

安装依赖：
```
npm install pdf-lib
```

---

## 快速参考

| 任务 | 方式 |
|------|------|
| 创建新 PDF | `pdf-lib`（见下方示例） |
| 合并 PDF | `pdf-lib` PDFDocument.copyPages |
| 拆分 PDF | `pdf-lib` 按页提取 |
| 旋转页面 | `pdf-lib` page.setRotation |
| 读取/分析内容 | 上传 PDF 让 AI 直接分析 |

---

## 创建新 PDF

```javascript
const { PDFDocument, rgb, StandardFonts, degrees } = require('pdf-lib');
const fs = require('fs');

async function createPdf() {
  const pdfDoc = await PDFDocument.create();

  // 嵌入字体（内置字体）
  const font = await pdfDoc.embedFont(StandardFonts.Helvetica);
  const boldFont = await pdfDoc.embedFont(StandardFonts.HelveticaBold);

  // 添加第一页（A4：595 x 842 pt）
  const page = pdfDoc.addPage([595, 842]);
  const { width, height } = page.getSize();

  // 标题
  page.drawText('报告标题', {
    x: 50,
    y: height - 80,
    size: 28,
    font: boldFont,
    color: rgb(0.12, 0.15, 0.38) // 深蓝色
  });

  // 分割线
  page.drawLine({
    start: { x: 50, y: height - 95 },
    end: { x: width - 50, y: height - 95 },
    thickness: 1,
    color: rgb(0.8, 0.8, 0.8)
  });

  // 正文
  page.drawText('这是 PDF 正文内容。可以包含多行文字和各种格式。', {
    x: 50,
    y: height - 130,
    size: 12,
    font,
    color: rgb(0, 0, 0)
  });

  // 保存
  const pdfBytes = await pdfDoc.save();
  fs.writeFileSync('output.pdf', pdfBytes);
  console.log('PDF 已生成: output.pdf');
}

createPdf();
```

---

## 多页文档

```javascript
const { PDFDocument, rgb, StandardFonts } = require('pdf-lib');
const fs = require('fs');

async function multiPagePdf() {
  const pdfDoc = await PDFDocument.create();
  const font = await pdfDoc.embedFont(StandardFonts.Helvetica);
  const boldFont = await pdfDoc.embedFont(StandardFonts.HelveticaBold);

  const sections = [
    { title: '第一章：概述', content: '本章介绍主要内容和背景信息。' },
    { title: '第二章：分析', content: '本章进行详细的数据分析。' },
    { title: '第三章：结论', content: '本章总结主要发现和建议。' }
  ];

  sections.forEach((section, i) => {
    const page = pdfDoc.addPage([595, 842]);
    const { width, height } = page.getSize();

    // 章节标题
    page.drawText(section.title, {
      x: 50, y: height - 80,
      size: 24, font: boldFont,
      color: rgb(0.12, 0.15, 0.38)
    });

    // 内容
    page.drawText(section.content, {
      x: 50, y: height - 130,
      size: 12, font,
      color: rgb(0, 0, 0)
    });

    // 页码
    page.drawText(`第 ${i + 1} 页`, {
      x: width / 2 - 20, y: 30,
      size: 10, font,
      color: rgb(0.5, 0.5, 0.5)
    });
  });

  const pdfBytes = await pdfDoc.save();
  fs.writeFileSync('multi_page.pdf', pdfBytes);
}

multiPagePdf();
```

---

## 合并多个 PDF

```javascript
const { PDFDocument } = require('pdf-lib');
const fs = require('fs');

async function mergePdfs(inputFiles, outputFile) {
  const mergedDoc = await PDFDocument.create();

  for (const filePath of inputFiles) {
    const pdfBytes = fs.readFileSync(filePath);
    const pdf = await PDFDocument.load(pdfBytes);
    const pages = await mergedDoc.copyPages(pdf, pdf.getPageIndices());
    pages.forEach(page => mergedDoc.addPage(page));
  }

  const mergedBytes = await mergedDoc.save();
  fs.writeFileSync(outputFile, mergedBytes);
  console.log(`已合并 ${inputFiles.length} 个文件 → ${outputFile}`);
}

// 使用示例
mergePdfs(['file1.pdf', 'file2.pdf', 'file3.pdf'], 'merged.pdf');
```

---

## 拆分 PDF

```javascript
const { PDFDocument } = require('pdf-lib');
const fs = require('fs');

async function splitPdf(inputFile) {
  const pdfBytes = fs.readFileSync(inputFile);
  const pdf = await PDFDocument.load(pdfBytes);
  const totalPages = pdf.getPageCount();

  for (let i = 0; i < totalPages; i++) {
    const newDoc = await PDFDocument.create();
    const [page] = await newDoc.copyPages(pdf, [i]);
    newDoc.addPage(page);

    const pageBytes = await newDoc.save();
    fs.writeFileSync(`page_${i + 1}.pdf`, pageBytes);
  }

  console.log(`已拆分为 ${totalPages} 个文件`);
}

splitPdf('input.pdf');
```

---

## 旋转页面

```javascript
const { PDFDocument, degrees } = require('pdf-lib');
const fs = require('fs');

async function rotatePage(inputFile, pageIndex, rotationDeg) {
  const pdfBytes = fs.readFileSync(inputFile);
  const pdfDoc = await PDFDocument.load(pdfBytes);

  const page = pdfDoc.getPage(pageIndex);
  page.setRotation(degrees(rotationDeg)); // 90, 180, 270

  const rotatedBytes = await pdfDoc.save();
  fs.writeFileSync('rotated.pdf', rotatedBytes);
}

rotatePage('input.pdf', 0, 90); // 旋转第一页 90 度
```

---

## 添加水印

```javascript
const { PDFDocument, rgb, StandardFonts, degrees } = require('pdf-lib');
const fs = require('fs');

async function addWatermark(inputFile, watermarkText) {
  const pdfBytes = fs.readFileSync(inputFile);
  const pdfDoc = await PDFDocument.load(pdfBytes);
  const font = await pdfDoc.embedFont(StandardFonts.HelveticaBold);

  const pages = pdfDoc.getPages();
  pages.forEach(page => {
    const { width, height } = page.getSize();
    page.drawText(watermarkText, {
      x: width / 4,
      y: height / 2,
      size: 60,
      font,
      color: rgb(0.8, 0.8, 0.8),
      opacity: 0.3,
      rotate: degrees(45)
    });
  });

  const watermarkedBytes = await pdfDoc.save();
  fs.writeFileSync('watermarked.pdf', watermarkedBytes);
}

addWatermark('input.pdf', '机密');
```

---

## 插入图片

```javascript
const { PDFDocument } = require('pdf-lib');
const fs = require('fs');

async function addImage(pdfFile, imageFile) {
  const pdfBytes = fs.readFileSync(pdfFile);
  const pdfDoc = await PDFDocument.load(pdfBytes);

  // 支持 PNG 和 JPG
  const imgBytes = fs.readFileSync(imageFile);
  const image = imageFile.endsWith('.png')
    ? await pdfDoc.embedPng(imgBytes)
    : await pdfDoc.embedJpg(imgBytes);

  const page = pdfDoc.getPage(0);
  const { width, height } = page.getSize();

  // 按比例缩放图片
  const imgDims = image.scale(0.5); // 缩放到 50%
  page.drawImage(image, {
    x: 50,
    y: height - imgDims.height - 100,
    width: imgDims.width,
    height: imgDims.height
  });

  const pdfBytesOut = await pdfDoc.save();
  fs.writeFileSync('with_image.pdf', pdfBytesOut);
}

addImage('input.pdf', 'logo.png');
```

---

## 提取页面信息

```javascript
const { PDFDocument } = require('pdf-lib');
const fs = require('fs');

async function getPdfInfo(filePath) {
  const pdfBytes = fs.readFileSync(filePath);
  const pdfDoc = await PDFDocument.load(pdfBytes);

  console.log(`总页数: ${pdfDoc.getPageCount()}`);
  console.log(`标题: ${pdfDoc.getTitle() || '未设置'}`);
  console.log(`作者: ${pdfDoc.getAuthor() || '未设置'}`);
  console.log(`创建时间: ${pdfDoc.getCreationDate()}`);

  pdfDoc.getPages().forEach((page, i) => {
    const { width, height } = page.getSize();
    console.log(`第 ${i + 1} 页: ${width.toFixed(0)} x ${height.toFixed(0)} pt`);
  });
}

getPdfInfo('document.pdf');
```

---

## 常用页面尺寸（pt 单位）

| 纸张 | 宽 (pt) | 高 (pt) |
|------|---------|---------|
| A4 | 595 | 842 |
| A3 | 842 | 1191 |
| US Letter | 612 | 792 |
| US Legal | 612 | 1008 |

---

## 内置字体

```javascript
const { StandardFonts } = require('pdf-lib');

// 可用字体
StandardFonts.Helvetica
StandardFonts.HelveticaBold
StandardFonts.HelveticaOblique
StandardFonts.HelveticaBoldOblique
StandardFonts.TimesRoman
StandardFonts.TimesRomanBold
StandardFonts.Courier
StandardFonts.CourierBold
```

**注意：内置字体不支持中文。** 如需中文，需嵌入支持中文的 TTF 字体：
```javascript
const fontBytes = fs.readFileSync('NotoSansCJK.ttf');
const customFont = await pdfDoc.embedFont(fontBytes);
```

---

## 依赖

```
npm install pdf-lib
```
