---
name: excel-file-processing
description: "在手机端使用 JavaScript（ExcelJS）创建和处理 Excel 文件，适用于 RikkaHub 环境。当用户说「Excel处理」「生成表格」「创建Excel」「手机Excel」时触发。"
---

# Excel 文件处理（手机版）

## 环境说明

手机端（RikkaHub）可执行 **JavaScript**，生成 Excel 文件使用 **ExcelJS**。

安装依赖：
```
npm install exceljs
```

---

## 输出质量要求

### 全部 Excel 文件

- 使用专业字体（Arial、Times New Roman 等）
- 每个 Excel 模型必须 **零公式错误**（#REF! #DIV/0! #VALUE! #N/A #NAME?）
- 修改模板时，严格匹配已有格式、风格和约定，已有约定始终优先

### 财务模型颜色规范

| 颜色 | 含义 |
|------|------|
| 蓝色文字 (0,0,255) | 硬编码输入值（用户会改变的数字） |
| 黑色文字 (0,0,0) | 所有公式和计算结果 |
| 绿色文字 (0,128,0) | 从同工作簿其他表引用的数据 |
| 红色文字 (255,0,0) | 外部文件链接 |
| 黄色背景 (255,255,0) | 需要注意的关键假设单元格 |

### 数字格式规范

- **年份**：文字字符串（"2024" 而非 "2,024"）
- **货币**：$#,##0 格式，表头注明单位（"Revenue ($mm)"）
- **零值**：用 "-" 显示，包括百分比
- **百分比**：默认保留一位小数（0.0%）
- **倍数**：0.0x 格式（EV/EBITDA、P/E 等）
- **负数**：用括号 (123)，不用负号 -123

---

## 创建新 Excel 文件（JavaScript）

```javascript
const ExcelJS = require('exceljs');

async function createExcel() {
  const workbook = new ExcelJS.Workbook();
  workbook.creator = 'Capy';
  workbook.created = new Date();

  const sheet = workbook.addWorksheet('Sheet1');

  // 添加表头行
  sheet.addRow(['项目', '金额', '备注']);

  // 设置表头样式
  const headerRow = sheet.getRow(1);
  headerRow.eachCell(cell => {
    cell.font = { bold: true, name: 'Arial', size: 11 };
    cell.fill = { type: 'pattern', pattern: 'solid', fgColor: { argb: 'FFD5E8F0' } };
    cell.alignment = { horizontal: 'center', vertical: 'middle' };
    cell.border = {
      top: { style: 'thin' },
      bottom: { style: 'thin' },
      left: { style: 'thin' },
      right: { style: 'thin' }
    };
  });

  // 添加数据行（蓝色=输入值）
  sheet.addRow(['收入 A', 1000, '来源: 2024年报']);
  sheet.addRow(['收入 B', 2000, '来源: 2024年报']);

  // 公式行（黑色=公式）
  const totalRow = sheet.addRow(['合计', { formula: 'SUM(B2:B3)' }, '']);
  totalRow.getCell(2).font = { color: { argb: 'FF000000' } }; // 黑色=公式

  // 设置列宽
  sheet.columns = [
    { width: 20 },
    { width: 15 },
    { width: 30 }
  ];

  await workbook.xlsx.writeFile('output.xlsx');
  console.log('Excel 文件已生成: output.xlsx');
}

createExcel();
```

---

## 关键规则：用公式，不用硬编码

**始终使用 Excel 公式，而不是用 JavaScript 计算后写死结果。**

### 错误做法
```javascript
// 在 JS 里算出结果后写死 — 错！
const total = 1000 + 2000;
sheet.getCell('B4').value = total; // 硬编码 3000
```

### 正确做法
```javascript
// 让 Excel 自己计算 — 对！
sheet.getCell('B4').value = { formula: 'SUM(B2:B3)' };
sheet.getCell('C5').value = { formula: '=(C4-C2)/C2' };
sheet.getCell('D6').value = { formula: 'AVERAGE(D2:D5)' };
```

---

## 格式化与样式

```javascript
const ExcelJS = require('exceljs');

async function formatExcel() {
  const workbook = new ExcelJS.Workbook();
  const sheet = workbook.addWorksheet('财务模型');

  // 蓝色文字 = 输入值
  function inputCell(cell, value) {
    cell.value = value;
    cell.font = { color: { argb: 'FF0000FF' }, name: 'Arial' };
  }

  // 黑色文字 = 公式
  function formulaCell(cell, formula) {
    cell.value = { formula };
    cell.font = { color: { argb: 'FF000000' }, name: 'Arial' };
  }

  // 数字格式
  sheet.getCell('B2').numFmt = '$#,##0;($#,##0);-'; // 货币，零显示为 -
  sheet.getCell('C2').numFmt = '0.0%';               // 百分比
  sheet.getCell('D2').numFmt = '0.0x';               // 倍数

  // 列宽
  sheet.getColumn('A').width = 25;
  sheet.getColumn('B').width = 15;

  // 冻结首行
  sheet.views = [{ state: 'frozen', ySplit: 1 }];

  await workbook.xlsx.writeFile('formatted.xlsx');
}

formatExcel();
```

---

## 读取分析现有 Excel

```javascript
const ExcelJS = require('exceljs');

async function readExcel(filePath) {
  const workbook = new ExcelJS.Workbook();
  await workbook.xlsx.readFile(filePath);

  workbook.eachSheet((sheet, id) => {
    console.log(`\n=== 工作表: ${sheet.name} ===`);
    sheet.eachRow((row, rowNum) => {
      if (rowNum <= 5) { // 只看前5行预览
        console.log(`行 ${rowNum}:`, row.values.slice(1).join(' | '));
      }
    });
  });
}

readExcel('input.xlsx');
```

---

## 多工作表

```javascript
const ExcelJS = require('exceljs');

async function multiSheet() {
  const workbook = new ExcelJS.Workbook();

  // 创建多个工作表
  const summary = workbook.addWorksheet('汇总');
  const details = workbook.addWorksheet('明细');
  const assumptions = workbook.addWorksheet('假设');

  // 假设表（输入值）
  assumptions.addRow(['假设项', '数值']);
  assumptions.addRow(['增长率', 0.15]);
  assumptions.getCell('B2').numFmt = '0.0%';

  // 明细表引用假设表（跨表公式）
  details.addRow(['基准收入', '增长后收入']);
  details.addRow([1000, { formula: 'A2*(1+假设!B2)' }]);

  // 汇总表
  summary.addRow(['总收入', { formula: '明细!B2' }]);

  await workbook.xlsx.writeFile('multi_sheet.xlsx');
}

multiSheet();
```

---

## 图表（Charts）

```javascript
const ExcelJS = require('exceljs');

async function addChart() {
  const workbook = new ExcelJS.Workbook();
  const sheet = workbook.addWorksheet('数据');

  // 添加数据
  sheet.addRow(['月份', '收入']);
  ['一月', '二月', '三月', '四月'].forEach((m, i) => {
    sheet.addRow([m, (i + 1) * 1000]);
  });

  // ExcelJS 支持基础图表（柱状图、折线图等）
  // 注意：图表支持需要商业版 ExcelJS 或手动写入 XML
  // 替代方案：在单元格中用 sparklines 或注释说明图表意图

  await workbook.xlsx.writeFile('chart.xlsx');
}

addChart();
```

---

## 公式验证清单

- [ ] 测试 2-3 个样本引用：确认公式引用正确单元格
- [ ] 列映射：确认 Excel 列与数据对应（第 64 列 = BL，不是 BK）
- [ ] 行偏移：Excel 行从 1 开始，数据第一行通常是第 2 行
- [ ] NaN 处理：检查空值和 null
- [ ] 除以零：检查分母不为零（#DIV/0!）
- [ ] 跨表引用：格式为 `Sheet1!A1`

---

## 常见错误处理

| 错误 | 原因 | 解决方式 |
|------|------|----------|
| #REF! | 无效单元格引用 | 检查引用范围是否正确 |
| #DIV/0! | 除以零 | 用 `IFERROR` 包裹或检查分母 |
| #VALUE! | 数据类型错误 | 确认参与计算的单元格都是数字 |
| #NAME? | 不认识的函数名 | 检查拼写，确认 ExcelJS 支持该公式 |
| #N/A | 查找未找到结果 | VLOOKUP/MATCH 未匹配到值 |

---

## 依赖

```
npm install exceljs
```
