# 第三方声明

本插件**没有打包或复制**任何第三方项目的源代码、字体、图标库或论文插图。核心 Python/SVG 渲染、期刊预设、审计规则和文档为原创实现。

## 运行时依赖（由用户环境提供）

| 依赖 | 用途 | 许可（以安装包为准） |
| --- | --- | --- |
| matplotlib | 数据图渲染 | PSF-based/BSD-compatible |
| numpy | 数值计算 | BSD-3-Clause |
| pandas | CSV/TSV 读取 | BSD-3-Clause |
| Pillow | 位图读写与压平 | HPND |
| PyMuPDF | PDF/SVG 读取与转换 | AGPL-3.0 或商业许可（用户需自行确认） |
| seaborn | 可选 | BSD-3-Clause |
| pywin32 | 可选，Illustrator COM | PSF-based |

如果 PyMuPDF 的 AGPL 许可不适合你的分发场景，可改用其他 SVG→PDF 工具；插件在缺少 PyMuPDF 时会降级，不会静默安装。

## 仅作为外部工具引用

- **Scientific Illustrator Agent**（`LY2260789/scientific-illustrator-agent`）：仓库当前未声明开源许可证。本插件不分发、不复制其代码；仅在用户自行安装后，由 `sci-figure-illustrator` 技能文档指导调用。
- **LiveFigure**（`tsinghua-fib-lab/LiveFigure`）：研究项目，许可以其仓库为准；本插件只借鉴“技能库 + 视觉诊断”的公开思路。
- **SciencePlots、tueplots、adjustText、statannotations、D2、Mermaid、Draw.io、Bioicons、Servier Medical Art 等**：仅作为调研与可选工作流参考；未打包其代码或素材。

## 图标

`render_diagram.py` 中的线稿图标（data/database/model/chart/...）为本插件用基础 SVG 几何图形原创绘制，不来自第三方图标库。

## 字体

插件不附带字体文件。渲染使用用户系统已安装的字体；期刊投稿前需确认字体许可允许嵌入与再分发。
