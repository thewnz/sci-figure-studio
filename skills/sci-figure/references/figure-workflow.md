# Figure Contract 与工作流

## 为什么先立契约

论文图常见的返工不是“不好看”，而是：图要证明的东西没说清、数据口径变了、字号/栏宽到投稿才发现不对。Figure Contract 把“科学内容 + 图面任务 + 期刊约束 + 证据来源”一次写清，后续生成和审计都围绕它执行。

## 契约字段

```json
{
  "schema": "scifig.figure/1.0",
  "id": "fig1",
  "type": "data",
  "venue": "nature",
  "layout": "single",
  "width_mm": 89,
  "height_mm": null,
  "title": "Ours improves accuracy on all three benchmarks",
  "purpose": "让读者一眼看出我们的方法在三个基准上均优于基线，且方差可接受。",
  "data": {
    "file": "results.csv",
    "x": "benchmark",
    "y": ["score"],
    "group": "method",
    "yerr": ["std"],
    "xlabel": "Benchmark",
    "ylabel": "Accuracy (%)",
    "highlight": "Ours"
  },
  "chart": {
    "kind": "grouped_bar",
    "palette": "okabe_ito",
    "annotate": true,
    "legend": "auto"
  },
  "style": {
    "font": "Arial",
    "font_pt": 7,
    "line_width_pt": 0.6,
    "grid": false
  },
  "export": ["svg", "pdf", "png"],
  "caption": "Figure 1. Accuracy on three benchmarks (mean ± s.d., n = 5).",
  "provenance": {
    "data_source": "experiments/2026-11-10/results.csv",
    "reference_figures": [],
    "notes": "误差线为标准差；未做显著性检验。"
  }
}
```

### 必填

- `id`、`type`（`data|diagram|concept`）、`venue`、`layout`。
- `purpose`：一句话说明图面任务。
- `data` 或 `diagram`：内容映射。
- `export`：至少包含 `svg` 或 `pdf`。
- `provenance.data_source`：数据/方法来源，便于复核。

### 推荐

- `caption`：独立图注；不要画进图。
- `title`：内部标题，不渲染进图。
- `constraints`：手工覆盖最小字号/最大高度。
- `reference_figures`：参考图来源（只学构图，不复制内容）。

## 三种图型

### data

- 输入：CSV/TSV 长表或宽表。
- 关键映射：`x`、`y`、`group`、`yerr`、`xlabel`、`ylabel`、`highlight`。
- 统计口径必须写清：mean ± s.d.、mean ± s.e.m.、95% CI、n。
- 缺失值不补造；缺失较多的组应在图注说明。

### diagram

```json
{
  "diagram": {
    "direction": "horizontal",
    "layout": "layered",
    "nodes": [
      { "id": "data", "label": "Raw data", "stage": 0, "kind": "io", "icon": "data" },
      { "id": "model", "label": "Proposed model", "stage": 1, "kind": "process", "icon": "model" },
      { "id": "eval", "label": "Evaluation", "stage": 2, "kind": "process", "icon": "chart" }
    ],
    "edges": [
      { "from": "data", "to": "model", "style": "solid", "arrow": "end" },
      { "from": "model", "to": "eval", "style": "solid", "arrow": "end" }
    ]
  }
}
```

- `stage` 决定列/行；同 stage 节点并排。
- `kind`：`process|decision|io|database|actor|note|terminal|group`。
- `icon`：`data|database|model|chart|experiment|microscope|molecule|cell|dna|brain|robot|cloud|server|document|gear|shield|check|warning|none`。
- `edge.style`：`solid|dashed`；`arrow`：`end|both|none`。
- 条件标签只写原文有依据的词（如 “pass / fail”）。


### 总览 + 局部放大（overview_detail）

适合方法框架图、graphical abstract、需要“先看全局再看关键步骤”的图。

```json
{
  "type": "concept",
  "style": { "theme": "rich", "font": "Arial", "font_pt": 7 },
  "diagram": {
    "composition": "overview_detail",
    "groups": [
      {
        "id": "g1", "label": "Spectral input", "color": "red",
        "overview_motif": { "type": "spectrum_strip", "direction": "vertical" },
        "detail": {
          "caption": "Raw spectra to normalization",
          "motifs": [
            { "id": "m1", "type": "spectrum_strip", "x": 0.03, "y": 0.08, "w": 0.22, "h": 0.50, "label": "raw" },
            { "id": "m2", "type": "waveform", "x": 0.30, "y": 0.12, "w": 0.27, "h": 0.36, "label": "SNR" },
            { "id": "m3", "type": "formula", "x": 0.24, "y": 0.78, "w": 0.60, "h": 0.12, "text": "x_i ← (x_i - μ)/σ" }
          ],
          "arrows": [{ "from": "m1", "to": "m2" }]
        }
      }
    ]
  }
}
```

- `groups`：2–4 组；每组对应一个顶部总览卡和一个底部放大面板。
- `group.color`：`red|green|blue|amber|purple|teal` 或十六进制；strict 会映射到色盲安全色。
- `overview_motif`：总览卡里的小图元，保持一眼可读。
- `detail.motifs`：归一化坐标 `x/y/w/h`（0–1），渲染器换算到面板内容区。
- `detail.arrows`：连接 motif 的流程箭头，`from/to` 必须引用 motif `id`。
- `style.theme`：`rich`（色带渐变/半透明/3D 堆叠）或 `strict`（白底细线，正式投稿）。
- 面板字母 `a/b/c` 自动生成；图注仍放契约/正文，不画进图。

### 图元类型

`spectrum_strip`、`tensor_stack`、`feature_matrix`、`network`、`formula`、`bar_group`、`chips`、`mini_table`、`waveform`、`heatmap`、`icon`。

图元是抽象科研符号，不生成分子结构或真实实验图像；需要真实结构图时用领域工具生成后再做矢量标注。

### concept

- 与 `diagram` 共用模型，`layout` 用 `radial|freeform|pipeline`。
- `freeform` 使用 0–1 归一化 `x/y/w/h`，适合复刻参考图构图。
- 图标只做语义提示，不替代文字；文字仍是主信息。

## 工作流检查点

1. **契约评审**：purpose 是否能被图独立回答？节点/数据是否都有来源？
2. **生成评审**：文字是否溢出/重叠？箭头是否穿过无关节点？坐标轴单位是否齐全？
3. **审计评审**：尺寸/DPI/字体/颜色是否过线？
4. **作者评审**：统计口径、图像完整性、术语、版权。

## 常见返工

| 症状 | 根因 | 修复 |
| --- | --- | --- |
| 投稿系统说 DPI 低 | 先画大图再缩，或 PNG 只有 72 dpi | 按最终栏宽建画布；线图改矢量 |
| 字体被替换/缺字 | 字体未嵌入或系统无该字体 | 用 Arial/Helvetica；PDF fonttype 42 |
| 图太小/太大 | 画布不是期刊栏宽 | 用预设宽度，禁止二次缩放 |
| 颜色看不清 | 红绿对比、只靠颜色 | Okabe–Ito + 线型/标记/直接标签 |
| 文字被裁 | 负边距/固定画布 | 自动换行 + 增加边距 |
| 流程图很“AI” | 彩色圆角卡片、渐变、装饰过多 | 白底、细线、少色、结构优先 |
