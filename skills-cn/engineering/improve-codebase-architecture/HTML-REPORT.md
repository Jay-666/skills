# HTML 报告格式

架构审查在操作系统临时目录中渲染为单一自包含 HTML 文件。Tailwind 和 Mermaid 均来自 CDN。Mermaid 能可靠处理图形形态图表；手工 div 和内联 SVG 处理更具编辑感的视觉效果（质量图、横截面）。混合使用两者，不要所有内容都依赖 Mermaid，否则会显得通用。

## 脚手架

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>架构审查 — {{仓库名称}}</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script type="module">
      import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
      mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose" });
    </script>
    <style>
      /* small custom layer for things Tailwind doesn't cover cleanly:
         dashed seam lines, hand-drawn-feeling arrow heads, etc. */
      .seam { stroke-dasharray: 4 4; }
      .leak { stroke: #dc2626; }
      .deep { background: linear-gradient(135deg, #0f172a, #1e293b); }
    </style>
  </head>
  <body class="bg-stone-50 text-slate-900 font-sans">
    <main class="max-w-5xl mx-auto px-6 py-12 space-y-12">
      <header>...</header>
      <section id="candidates" class="space-y-10">...</section>
      <section id="top-recommendation">...</section>
    </main>
  </body>
</html>
```

## 页头

仓库名称、日期和紧凑图例：实线框 = 模块，虚线 = 边界点，红色箭头 = 泄漏，粗深色框 = 深模块。不要引言段落，直接进入候选项。

## 候选卡片

图表承担主要信息。文字应稀疏、平实，并直接使用术语表（来自 `/codebase-design` skill）术语。

每个候选项是一个 `<article>`：

- **标题**：简短，命名加深动作（例如“折叠订单接收管线”）。
- **徽章行**：建议强度（`Strong` = 翡翠绿，`Worth exploring` = 琥珀色，`Speculative` = 石板灰），加上依赖类别标签（`in-process`、`local-substitutable`、`ports & adapters`、`mock`）。
- **文件**：等宽列表，`font-mono text-sm`。
- **前后对比图**：中心内容。两列并排。见下方模式。
- **问题**：一句话，说明痛点。
- **方案**：一句话，说明改变什么。
- **收益**：每项不超过 6 个词的要点，例如“测试命中一个接口”“定价逻辑不再泄漏”“删除 4 个浅包装器”。
- **ADR 提示**（若适用）：琥珀色调框中的一行。

不要解释性段落。若图表需要一段文字才能理解，应重绘图表。

## 图表模式

选择适合候选项的模式，并混合使用。不要让每张图都相同，变化是目的的一部分。

### Mermaid 图（依赖和调用流的主力）

当重点是“X 调用 Y 调用 Z，看看这团混乱”时，使用 Mermaid `flowchart` 或 `graph`。将其包裹在带 Tailwind 样式的卡片中，避免显得突兀。使用 classDef 将泄漏边着为红色，将深模块着为深色。序列图适合表达“之前：6 次往返；之后：1 次”。

```html
<div class="rounded-lg border border-slate-200 bg-white p-4">
  <pre class="mermaid">
    flowchart LR
      A[OrderHandler] --> B[OrderValidator]
      B --> C[OrderRepo]
      C -.leak.-> D[PricingClient]
      classDef leak stroke:#dc2626,stroke-width:2px;
      class C,D leak
  </pre>
</div>
```

### 手工框和箭头（当 Mermaid 布局与你对抗时）

将模块呈现为带边框和标签的 `<div>`。箭头为相对容器上绝对定位的内联 SVG `<line>` 或 `<path>` 元素。当希望“之后”图表看起来像一个粗边框深模块、内部灰化时使用此方式，Mermaid 无法以正确视觉权重渲染它。

### 横截面（适合分层浅度）

堆叠水平条带（`h-12 border-l-4`）展示调用穿过的层。之前：6 个薄层各自无所作为。之后：1 个标有整合职责的粗条带。

### 质量图（适合“接口与实现一样宽”）

每个模块两个矩形，一个表示接口表面积，一个表示实现。之前：接口矩形几乎和实现矩形一样高（浅）。之后：接口矩形短，实现矩形高（深）。

### 调用图折叠

之前：函数调用树呈现为嵌套框。之后：同一棵树折叠为一个框，现已内部化的调用在其中淡化显示。

## 样式指南

- 偏编辑感，而非企业仪表盘。留出充足空白。标题可选衬线字体（`font-serif` 与石色或石板色搭配良好）。
- 谨慎使用颜色：一个强调色（翡翠绿或靛蓝）加上表示泄漏的红色和表示警告的琥珀色。
- 保持图表约 320px 高，使前后对比可舒适并排且无需滚动。
- 图表内模块标签使用 `text-xs uppercase tracking-wider`，它们应读起来像示意图，而非 UI。
- 脚本仅限 Tailwind CDN 和 Mermaid ESM 导入。报告其他部分静态，无应用代码，无 Mermaid 自身渲染之外的交互。

## 首要建议章节

一张更大的卡片：候选项名称、一句原因、链接到其卡片的锚点。仅此而已。

## 语气

使用平实、简洁的中文，但架构名词和动词直接来自 `/codebase-design` skill。简洁不是偏移的借口。

**准确使用：**模块、接口、实现、深度、深、浅、边界点、适配器、杠杆、局部性。

**绝不替代为：**组件、服务、单元（代替模块）；API、签名（代替接口）；boundary（代替边界点）；层、包装器（当想表达模块时）。

**符合该风格的表述：**

- “订单接收模块是浅的，接口几乎与实现相匹配。”
- “定价跨越边界点泄漏。”
- “加深：一个接口，一个测试位置。”
- “两个适配器证明边界点合理：生产使用 HTTP，测试使用内存。”

**收益要点**使用术语表术语命名收益，例如*“局部性：缺陷集中在一个模块”*、*“杠杆：一个接口，N 个调用点”*、*“接口缩小，实现吸收包装器”*。不要写*“更容易维护”*或*“更干净的代码”*，这些词不在术语表中，也不值得占据位置。

不要模棱两可，不要铺垫，不要说“值得注意的是……”。若一句话可以成为要点，就让它成为要点；若要点可以删除，就删除它。若术语不在 `/codebase-design` 术语表中，在发明新术语前先使用其中已有的术语。
