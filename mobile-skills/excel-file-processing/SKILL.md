---
name: excel-file-processing
description: "在手机端使用纯 JavaScript 创建 CSV/Excel 数据，适用于 RikkaHub QuickJS 环境（无需 npm）。当用户说「Excel处理」「生成表格」「创建表格」「数据表」时触发。"
---

# Excel / CSV 数据处理（手机版，QuickJS）

## 环境说明

RikkaHub 使用 **QuickJS** 引擎执行 JavaScript，有以下限制：

- **不支持** `require()`、npm 包、`fs` 模块、Node.js API
- **只支持** 纯 ES2020+ JavaScript
- 输出通过 `console.log()` 返回文本

**工作流程：**
1. 运行脚本 → 输出 CSV 文本
2. 复制输出内容 → 保存为 `.csv` 文件
3. 用 Excel / Numbers / Google Sheets 打开

---

## 核心函数：纯 JS CSV 构建器

```javascript
// CSV 转义：处理包含逗号、换行、引号的字段
function escapeCSV(value) {
  if (value === null || value === undefined) return '';
  const str = String(value);
  if (str.includes(',') || str.includes('"') || str.includes('\n')) {
    return '"' + str.replace(/"/g, '""') + '"';
  }
  return str;
}

// 将一行数据转换为 CSV 行
function rowToCSV(rowArray) {
  return rowArray.map(escapeCSV).join(',');
}

// 将二维数组构建为完整 CSV 字符串
function buildCSV(rows) {
  return rows.map(rowToCSV).join('\n');
}

// 公式标注（CSV 本身不执行公式，用注释标注意图）
// 在 Excel 中打开 .csv 后可手动输入公式
// 示例：标注 "=SUM(B2:B5)" 提示用户在 Excel 中输入
function formulaNote(formula) {
  return `[公式: ${formula}]`;
}
```

---

## 财务模型颜色规范

> 本节为 AI 生成财务数据时的格式指引，非代码。

| 颜色 | 含义 |
|------|------|
| 蓝色文字 | 硬编码输入值（用户会修改的数字） |
| 黑色文字 | 所有公式和计算结果 |
| 绿色文字 | 从同工作簿其他表引用的数据 |
| 红色文字 | 外部文件链接 |
| 黄色背景 | 需要注意的关键假设单元格 |

> CSV 不支持颜色，可在导入 Excel 后按以上规范手动设置，或在生成 CSV 时在注释列标注类型（"输入值" / "公式"）。

---

## 数字格式规范

- **年份**：文字字符串（"2024" 而非 2024）
- **货币**：数字直接输出，表头注明单位（"收入 ($万)"）
- **零值**：用 "-" 显示
- **百分比**：输出为小数（0.15），在 Excel 中设置 `0.0%` 格式
- **倍数**：追加 "x" 后缀（"3.2x"）
- **负数**：用括号表示（"(123)"），不用负号

---

## 示例：创建基础数据表

```javascript
function escapeCSV(value) {
  if (value === null || value === undefined) return '';
  const str = String(value);
  if (str.includes(',') || str.includes('"') || str.includes('\n')) {
    return '"' + str.replace(/"/g, '""') + '"';
  }
  return str;
}

function buildCSV(rows) {
  return rows.map(row => row.map(escapeCSV).join(',')).join('\n');
}

const rows = [
  ['项目', '金额 (元)', '占比', '备注'],
  ['收入 A', 100000, '40%', '来源: 2024年报'],
  ['收入 B', 150000, '60%', '来源: 2024年报'],
  ['', '', '', ''],
  ['合计', 250000, '100%', '[公式: =SUM(B2:B3)]'],
];

const csv = buildCSV(rows);
console.log(csv);
```

**输出示例：**
```
项目,金额 (元),占比,备注
收入 A,100000,40%,来源: 2024年报
收入 B,150000,60%,来源: 2024年报
,,,
合计,250000,100%,[公式: =SUM(B2:B3)]
```

---

## 示例：财务模型表（多区块）

```javascript
function escapeCSV(value) {
  if (value === null || value === undefined) return '';
  const str = String(value);
  if (str.includes(',') || str.includes('"') || str.includes('\n')) {
    return '"' + str.replace(/"/g, '""') + '"';
  }
  return str;
}

function buildCSV(rows) {
  return rows.map(row => row.map(escapeCSV).join(',')).join('\n');
}

// 假设区块（输入值）
const assumptions = [
  ['【假设区块】', '', '', ''],
  ['项目', '数值', '类型', '说明'],
  ['收入增长率', '15%', '输入值', '用户可修改'],
  ['毛利率', '60%', '输入值', '用户可修改'],
  ['折现率', '10%', '输入值', 'DCF 假设'],
  ['', '', '', ''],
];

// 收入预测区块
const years = ['2024', '2025', '2026', '2027', '2028'];
const baseRevenue = 1000;
const growthRate = 0.15;

const revenueHeader = ['【收入预测 ($万)】', ...years];
const revenueRow = ['收入'];
years.forEach((yr, i) => {
  const rev = Math.round(baseRevenue * Math.pow(1 + growthRate, i));
  revenueRow.push(rev);
});
const grossProfitRow = ['毛利润'];
years.forEach((yr, i) => {
  const rev = Math.round(baseRevenue * Math.pow(1 + growthRate, i));
  grossProfitRow.push(Math.round(rev * 0.6));
});
const ebitdaRow = ['EBITDA'];
years.forEach((yr, i) => {
  const rev = Math.round(baseRevenue * Math.pow(1 + growthRate, i));
  ebitdaRow.push(Math.round(rev * 0.3));
});

const financials = [
  revenueHeader,
  revenueRow,
  grossProfitRow,
  ebitdaRow,
  ['', ...years.map(() => '')],
  ['同比增长', '-', ...years.slice(1).map((yr, i) => {
    const prev = Math.round(baseRevenue * Math.pow(1 + growthRate, i));
    const curr = Math.round(baseRevenue * Math.pow(1 + growthRate, i + 1));
    return ((curr - prev) / prev * 100).toFixed(1) + '%';
  })],
];

const allRows = [...assumptions, ...financials];
const csv = buildCSV(allRows);
console.log(csv);
```

---

## 示例：统计汇总表

```javascript
function escapeCSV(value) {
  if (value === null || value === undefined) return '';
  const str = String(value);
  if (str.includes(',') || str.includes('"') || str.includes('\n')) {
    return '"' + str.replace(/"/g, '""') + '"';
  }
  return str;
}

function buildCSV(rows) {
  return rows.map(row => row.map(escapeCSV).join(',')).join('\n');
}

// 模拟数据集
const data = [
  { 部门: '销售部', 姓名: '张三', 季度: 'Q1', 业绩: 85000 },
  { 部门: '销售部', 姓名: '李四', 季度: 'Q1', 业绩: 92000 },
  { 部门: '技术部', 姓名: '王五', 季度: 'Q1', 业绩: 78000 },
  { 部门: '技术部', 姓名: '赵六', 季度: 'Q1', 业绩: 88000 },
  { 部门: '市场部', 姓名: '孙七', 季度: 'Q1', 业绩: 95000 },
];

// 按部门统计
const deptStats = {};
data.forEach(item => {
  if (!deptStats[item.部门]) {
    deptStats[item.部门] = { 总业绩: 0, 人数: 0 };
  }
  deptStats[item.部门].总业绩 += item.业绩;
  deptStats[item.部门].人数 += 1;
});

// 明细表
const detailRows = [
  ['=== 业绩明细 ===', '', '', ''],
  ['部门', '姓名', '季度', '业绩 (元)'],
  ...data.map(d => [d.部门, d.姓名, d.季度, d.业绩]),
  ['', '', '', ''],
];

// 汇总表
const totalRevenue = data.reduce((sum, d) => sum + d.业绩, 0);
const avgRevenue = Math.round(totalRevenue / data.length);

const summaryRows = [
  ['=== 部门汇总 ===', '', '', ''],
  ['部门', '人数', '总业绩 (元)', '人均业绩 (元)'],
  ...Object.entries(deptStats).map(([dept, stat]) => [
    dept,
    stat.人数,
    stat.总业绩,
    Math.round(stat.总业绩 / stat.人数)
  ]),
  ['', '', '', ''],
  ['全公司合计', data.length, totalRevenue, avgRevenue],
];

const csv = buildCSV([...detailRows, ...summaryRows]);
console.log(csv);
```

---

## 使用说明

1. **运行脚本** → 在 RikkaHub 中执行，获得 CSV 文本输出
2. **复制输出内容** → 选中全部输出文字并复制
3. **保存为 .csv** → 粘贴到文本编辑器，保存文件名以 `.csv` 结尾
4. **打开文件** → 用 Excel / Numbers / Google Sheets 打开
5. **添加格式** → 按财务模型颜色规范手动设置单元格颜色和数字格式
6. **输入公式** → 将标注 `[公式: =...]` 的单元格替换为实际 Excel 公式
