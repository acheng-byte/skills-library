---
name: ppt-file-processing
description: "在手机端使用 JavaScript（pptxgenjs）创建和处理 PPT 幻灯片，适用于 RikkaHub 环境。当用户说「PPT处理」「制作幻灯片」「生成PPT」「手机PPT」时触发。"
---

# PPT 文件处理（手机版）

## 环境说明

手机端（RikkaHub）可执行 **JavaScript**，创建 PPT 使用 **pptxgenjs** 包。

安装依赖：
```
npm install pptxgenjs
```

---

## 快速参考

| 任务 | 方式 |
|------|------|
| 创建新演示文稿 | 用 `pptxgenjs`（见下方示例） |
| 读取/分析内容 | 上传 .pptx 让 AI 直接分析 |

---

## 创建演示文稿

```javascript
const pptxgen = require('pptxgenjs');

const prs = new pptxgen();

// 设置幻灯片尺寸（宽屏 16:9）
prs.layout = 'LAYOUT_WIDE'; // 33.87cm x 19.05cm

// 封面页
const slide1 = prs.addSlide();
slide1.background = { color: '1E2761' }; // 深蓝色背景

slide1.addText('项目标题', {
  x: 1, y: 3, w: '80%', h: 1.5,
  fontSize: 44, bold: true,
  color: 'FFFFFF',
  align: 'center'
});

slide1.addText('副标题 · 2026年', {
  x: 1, y: 5, w: '80%', h: 0.8,
  fontSize: 20, color: 'CADCFC',
  align: 'center'
});

// 内容页
const slide2 = prs.addSlide();
slide2.background = { color: 'FFFFFF' };

// 标题
slide2.addText('第一章：核心观点', {
  x: 0.5, y: 0.3, w: '90%', h: 0.7,
  fontSize: 36, bold: true, color: '1E2761'
});

// 要点列表
const bullets = [
  '核心发现一：重要观点说明',
  '核心发现二：数据支撑内容',
  '核心发现三：结论与建议'
];
bullets.forEach((text, i) => {
  slide2.addText('• ' + text, {
    x: 0.8, y: 1.5 + i * 0.8, w: '85%', h: 0.7,
    fontSize: 16, color: '36454F'
  });
});

// 保存
prs.writeFile({ fileName: 'presentation.pptx' })
  .then(() => console.log('PPT 已生成: presentation.pptx'));
```

---

## 设计规范

### 颜色搭配

**不要用默认蓝色，选适合主题的颜色：**

| 主题 | 主色 | 辅色 | 强调色 |
|------|------|------|--------|
| 商务深沉 | `1E2761` 深蓝 | `CADCFC` 冰蓝 | `FFFFFF` 白 |
| 森林沉稳 | `2C5F2D` 深绿 | `97BC62` 苔绿 | `F5F5F5` 米白 |
| 活力珊瑚 | `F96167` 珊瑚 | `F9E795` 金黄 | `2F3C7E` 深蓝 |
| 暖陶土 | `B85042` 陶土 | `E7E8D1` 沙色 | `A7BEAE` 鼠尾草 |
| 极简炭黑 | `36454F` 炭灰 | `F2F2F2` 白 | `212121` 黑 |

**颜色法则：一种颜色主导（60-70%），1-2 种辅助色，一种强调色。**

### 字体搭配

| 标题字体 | 正文字体 |
|----------|----------|
| Georgia | Calibri |
| Arial Black | Arial |
| Calibri | Calibri Light |
| Trebuchet MS | Calibri |

| 元素 | 字号 |
|------|------|
| 幻灯片标题 | 36-44pt 加粗 |
| 章节标题 | 20-24pt 加粗 |
| 正文 | 14-16pt |
| 注释 | 10-12pt 灰色 |

### 布局选项

- **双栏**：文字左侧，图示右侧
- **图标行**：彩色圆圈图标 + 粗标题 + 说明文字
- **2×2 / 2×3 网格**：一侧图片，另一侧内容块
- **大数字**：60-72pt 超大数字 + 小标签说明

### 每张幻灯片必须有视觉元素

**禁止纯文字幻灯片。** 每页都需要图形、图标、数据图表或形状。

---

## 完整示例：双栏内容页

```javascript
const slide = prs.addSlide();
slide.background = { color: 'F8F9FA' };

// 标题
slide.addText('关键数据对比', {
  x: 0.5, y: 0.2, w: '90%', h: 0.6,
  fontSize: 36, bold: true, color: '1E2761'
});

// 左栏
slide.addShape(prs.ShapeType.rect, {
  x: 0.5, y: 1.1, w: 4.5, h: 5.2,
  fill: { color: '1E2761' },
  line: { color: '1E2761' }
});
slide.addText('2023年\n\n$1.2B\n收入', {
  x: 0.5, y: 1.1, w: 4.5, h: 5.2,
  fontSize: 18, color: 'FFFFFF',
  align: 'center', valign: 'middle',
  bold: true
});

// 右栏
slide.addShape(prs.ShapeType.rect, {
  x: 5.3, y: 1.1, w: 4.5, h: 5.2,
  fill: { color: 'CADCFC' },
  line: { color: 'CADCFC' }
});
slide.addText('2024年\n\n$1.8B\n收入 +50%', {
  x: 5.3, y: 1.1, w: 4.5, h: 5.2,
  fontSize: 18, color: '1E2761',
  align: 'center', valign: 'middle',
  bold: true
});
```

---

## 常见布局：大数字统计

```javascript
const slide = prs.addSlide();
slide.background = { color: 'FFFFFF' };

slide.addText('核心指标', {
  x: 0.5, y: 0.3, w: '90%', h: 0.6,
  fontSize: 36, bold: true, color: '1E2761'
});

// 三个大数字
const stats = [
  { num: '2.4x', label: 'ROI 回报倍数' },
  { num: '68%', label: '客户留存率' },
  { num: '$12M', label: '累计营收' }
];

stats.forEach((stat, i) => {
  const x = 0.5 + i * 3.5;
  slide.addText(stat.num, {
    x, y: 1.8, w: 3, h: 1.5,
    fontSize: 60, bold: true, color: '1E2761',
    align: 'center'
  });
  slide.addText(stat.label, {
    x, y: 3.4, w: 3, h: 0.6,
    fontSize: 14, color: '666666',
    align: 'center'
  });
});
```

---

## 禁止事项

- **不要重复相同布局** — 每张幻灯片变换布局
- **正文不要居中** — 段落和列表左对齐，只有标题居中
- **不要用默认蓝色** — 选择匹配主题的颜色
- **不要纯文字幻灯片** — 加图形、图标或图表
- **不要用标题下划线装饰线** — 这是典型 AI 生成特征
- **封面和结尾用深色背景** — 内容页用浅色（"三明治"结构）

---

## QA 验证

生成后回顾：
- [ ] 字体大小对比足够（标题 36pt+ vs 正文 14-16pt）
- [ ] 颜色对比度够强（文字 vs 背景）
- [ ] 每张有视觉元素
- [ ] 没有遗留占位符文字
- [ ] 间距一致（0.3-0.5" 之间）

---

## 依赖

```
npm install pptxgenjs
```
