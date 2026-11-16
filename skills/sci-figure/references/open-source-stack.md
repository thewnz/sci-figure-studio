# 开源生态调研与采用边界

核对日期：2026-11-16。以下基于公开 README、论文与仓库声明；**许可证以各仓库当前 LICENSE 为准**。本插件没有复制这些项目的源码或素材。

## 数据图与样式

| 项目 | 作用 | 许可证（公开声明） | 本插件如何采用 |
| --- | --- | --- | --- |
| [matplotlib](https://github.com/matplotlib/matplotlib) | Python 绘图基座 | PSF-based/BSD-compatible | 直接依赖；自写样式与渲染 |
| [seaborn](https://github.com/mwaskom/seaborn) | 统计图 | BSD-3-Clause | 可选依赖；不复制代码 |
| [SciencePlots](https://github.com/garrettj403/SciencePlots) | 期刊风格 rcParams | MIT | 借鉴“按图宽/黑白/色盲切换样式”的思路 |
| [tueplots](https://github.com/pnkraemer/tueplots) | LaTeX 图尺寸与字体 | MIT | 借鉴“按栏宽反推 figsize”的做法 |
| [adjustText](https://github.com/Phlya/adjustText) | 标签避让 | MIT | 记录为可选增强，未打包 |
| [statannotations](https://github.com/trevismd/statannotations) | 显著性标注 | MIT | 借鉴“标注必须有检验依据”的约束 |
| [colorcet](https://github.com/holoviz/colorcet) | 感知均匀色图 | BSD-3-Clause | 色板选择参考 |
| [cmcrameri](https://github.com/callumrollo/cmcrameri) | 科学色图 | MIT（以仓库为准） | 色图选择参考 |
| [daltonlens](https://github.com/DaltonLens/DaltonLens) | 色盲模拟 | BSD-3-Clause（以仓库为准） | 审计中使用自写的近似 CVD 矩阵，不复制其代码 |

## 流程图、概念图与可编辑矢量

| 项目 | 作用 | 许可证（公开声明） | 本插件如何采用 |
| --- | --- | --- | --- |
| [Mermaid](https://github.com/mermaid-js/mermaid) | 文本到图 | MIT | 导出 `.mmd` 文本，不打包渲染器 |
| [Draw.io](https://github.com/jgraph/drawio) | 可编辑图编辑器 | Apache-2.0 | 导出 `.drawio` XML，便于手工微调 |
| [D2](https://github.com/terrastruct/d2) | 文本化图源 + 布局 | MPL-2.0 | 只作为可选外部工具记录，不打包 |
| [Graphviz](https://gitlab.com/graphviz/graphviz) | DOT 布局 | EPL-2.0 | 作为可选外部工具记录，不打包 |
| [TikZ/PGF](https://github.com/pgf-tikz/pgf) | LaTeX 矢量图 | GPL-2.0 | 文档提及，不生成 TikZ |
| [Excalidraw](https://github.com/excalidraw/excalidraw) | 手绘白板 | MIT | 仅记录，不作为正式图默认 |
| [Bioicons](https://github.com/duerrsimon/bioicons) | 生物/化学 SVG 图标 | 逐图标 CC0/CC BY/MIT | 只记录为可选素材源；使用时逐图标署名 |
| [Servier Medical Art](https://smart.servier.com/) | 医学插图 | CC BY 4.0 | 只记录为可选素材源，需署名 |

## 科研图 Agent / 技能

| 项目 | 核心做法 | 许可证/状态 | 采用边界 |
| --- | --- | --- | --- |
| [academic-plotting](https://github.com/Orchestra-Research/AI-Research-SKILLs/tree/main/20-ml-paper-writing/academic-plotting) | 从论文上下文提取结构；数据图用 matplotlib，架构图用 Gemini | MIT | 借鉴“图型路由”和“图尺寸预设”；不照搬其 AI 出图流程 |
| [K-Dense scientific-visualization](https://github.com/K-Dense-AI/scientific-agent-skills/tree/main/skills/scientific-visualization) | 数据事实与呈现分离、字体/色觉/输出检查 | MIT | 借鉴质量门；代码原创 |
| [Scientific-Figure-Design](https://github.com/qhy991/Scientific-Figure-Design) | Claim-first Figure Contract；JSON → Draw.io | 以仓库为准 | 借鉴“先写图要证明什么”和统一色板令牌 |
| [AutoFigure-Edit](https://github.com/ResearAI/AutoFigure-Edit) | 位图草稿 → 分割 → 可编辑 SVG | 以仓库为准 | 借鉴“草稿/矢量分层”，不接入其重依赖 |
| [FigTree](https://github.com/yepengliu/FigTree) | 复杂图递归拆成 SVG 子图 | 以仓库为准 | 借鉴局部修复思路 |
| [AutoIllustrate](https://github.com/SakanaAI/autoillustrate) | Plan → 分面板 → 检查 → 合成 | 以仓库为准 | 借鉴分阶段与视觉复核 |
| [PaperBanana](https://github.com/dwzhu-pku/PaperBanana) | 检索参考图 → 规划 → 绘制 → 评审 | 以仓库为准 | 借鉴参考图与视觉评审分离 |
| [LiveFigure](https://github.com/tsinghua-fib-lab/LiveFigure) | VLM Agent + 标准化绘图技能 → 可编辑 PPTX；视觉诊断迭代 | 论文/代码以仓库为准 | 借鉴“技能库 + 经验约束 + 视觉诊断”三层；本插件用 SVG 而非 PPTX |
| [Scientific Illustrator Agent](https://github.com/LY2260789/scientific-illustrator-agent) | FigureSpec → Python COM/ExtendScript → Illustrator 原生矢量与可编辑文本 | **未声明许可证** | 只作为用户自行安装的外部后端；不分发、不复制代码 |

## 关键结论

1. **可编辑性优先**：LiveFigure 的实验显示，像素生成模型即使美观，也难达到“少量编辑即可投稿”；结构化、代码驱动的矢量路线更适合正式图。
2. **技能库比自由生成稳**：把画框、连线、文字封装成受验证的原语，比让模型直接写长代码更可靠。
3. **视觉诊断是必要闭环**：生成后必须看实际渲染，检查遮挡、越界、错位。
4. **不要迷信单一引擎**：数据图、流程图、概念图的最优工具不同；插件按图型路由。
5. **许可证要逐仓库核对**：无 LICENSE 不等于可自由使用；第三方图标/论文图/字体另算。

## 本插件的原创部分

- Figure Contract schema 与校验器。
- 期刊预设、审计规则与评分。
- matplotlib 渲染与样式映射。
- 原生 SVG 分层布局、节点形状、图标、Draw.io/Mermaid 导出。
- 色盲/灰度近似检查与报告。
- 所有文档、命令与技能流程。

未复制任何上游源码或素材。若后续要引入上游代码，必须逐项补充 LICENSE 与 THIRD_PARTY_NOTICES。
