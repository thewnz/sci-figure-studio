---
name: sci-figure-illustrator
description: 把科研图交接为 Adobe Illustrator 可编辑矢量。用户要求 .ai 文件、在 Illustrator 里改字/改线、可编辑 native vector、FigureSpec，或询问 Scientific Illustrator Agent 如何接入时使用。默认先交付 SVG/PDF；仅在检测到本机 Illustrator 时执行导出，并明确报告未验证项。
argument-hint: "给出要交接的 SVG/PDF 路径，以及是否需要 .ai、PNG 预览或逐元素绘制演示"
---

# Illustrator 可编辑矢量交接

Illustrator 是后期精修工具，不是默认绘图引擎。优先交付 **SVG/PDF**：文字、路径、箭头已是可编辑对象，Illustrator 可直接打开。只有用户明确要 `.ai` 时才进入 COM 导出。

## 三条路径

### A. 默认：SVG/PDF 直接打开（推荐）

- `sci-figure` 生成的 SVG 使用 `<text>` 与独立 `<g>`，不是位图描摹。
- PDF 使用嵌入字体（matplotlib `pdf.fonttype=42`），文字不转曲。
- 在 Illustrator 中打开后，用“文字工具”应能直接改字；用“直接选择工具”应能改单个节点/箭头。
- 交付前用 `sci-figure-audit` 确认字号、字体和嵌入。

### B. 可选：Scientific Illustrator Agent（外部工具）

上游项目：`https://github.com/LY2260789/scientific-illustrator-agent`

能力：AI Agent 把需求/参考图转成经校验的 FigureSpec JSON，再由 Python → Windows COM → ExtendScript 在 Illustrator 中生成原生矢量对象和可编辑文本；输出 `.ai`、PNG 预览、规格与渲染日志。

使用前提：

- Windows 10/11，交互式桌面会话。
- 本机已安装并激活 Adobe Illustrator（作者验证过 Illustrator 2022；2020 只是设计目标）。
- Python 3.9+，`pywin32`、`pydantic`、`Pillow`。
- **该仓库当前未声明开源许可证**。因此本插件不分发、不复制它的代码，只会在用户自行安装后调用。商用/再分发前必须自行联系作者确认许可。

接入方式：

1. 用户自行 clone 并安装依赖（见其 README）。
2. 设置环境变量 `SCI_ILLUSTRATOR_AGENT` 指向其仓库根目录，或把路径告诉 Agent。
3. Agent 先读该仓库的 `SKILL.md`、`docs/input_modes.md`、`src/models/figure_spec.py`，再按其真实 schema 写 FigureSpec。
4. 先 `--validate-only`，再渲染到**新输出文件**；不覆盖用户已有文档。
5. 检查导出的 PNG、对象数、文本数和 warning，再交付。

不要凭记忆编造其 CLI 参数；以该仓库当前 `SKILL.md` 为准。

### C. 可选：本插件的轻量 COM 导出（实验性）

本插件自带 `scripts/export_ai.py`，只做一件事：把已有 SVG/PDF 在 Illustrator 中打开，另存为 `.ai` 并导出 PNG。它不生成图、不做 OCR、不改科学内容。

```bash
python <skill-dir>/scripts/check_illustrator.py
python <skill-dir>/scripts/export_ai.py --input fig.pdf --out fig.ai --png fig.png
```

安全约束：

- 仅在 Windows + 已安装 Illustrator 时运行。
- 永远新建输出文件，不覆盖已有 `.ai`。
- 不结束 Illustrator 进程；遇到模态对话框先让用户处理。
- 导出后必须打开 PNG 人工检查，并报告对象/文本是否仍可编辑。
- 当前环境未安装 Illustrator 时，只能报告“环境不可用”，不能声称导出成功。

## 推荐交接格式

| 需求 | 格式 | 说明 |
| --- | --- | --- |
| 期刊投稿 | PDF/EPS | 矢量、字体嵌入、RGB |
| 后期精修 | SVG | 文本与分组保留，Illustrator/Figma/Inkscape 可开 |
| 原生 AI | `.ai` | 仅在本机 Illustrator 可用时 |
| 预览/审稿 | PNG | 300–600 dpi，不用于最终线图 |
| 可继续排版 | Draw.io | 节点/连线可拖拽 |

## 文字与字体

- 英文标签优先 Arial/Helvetica；ML 会议可用 Times New Roman；化学式/希腊字母用 Symbol 或正确 Unicode。
- Nature 明确要求：不要 outline 文字，嵌入 TrueType 2 或 42；最大 7 pt、最小 5 pt。
- 中文标签需要 CJK 字体（如 Microsoft YaHei/SimHei）；多数英文期刊要求图内文字用 Arial/Helvetica。若必须用中文，先确认期刊政策，必要时提供英文版或双语版。
- 交接前记录字体清单；打开 `.ai` 后检查是否出现缺字方框。

## 完成报告

必须包含：

- 使用的路径（A/B/C）。
- 输入与输出文件绝对路径。
- Illustrator/外部工具版本（若已知）。
- 对象数、文本数、warning（若外部工具提供）。
- 哪些项目已实际验证，哪些未验证（例如“未安装 Illustrator，未执行导出”）。
- 是否仍需作者在 Illustrator 中人工检查字体、对齐和图层。
