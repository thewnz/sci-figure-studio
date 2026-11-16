---
name: sci-figure
description: 面向顶刊顶会投稿的科研绘图工作流。用户要画数据图、流程图、方法示意图、概念图、graphical abstract，或要求符合 Nature/Science/Cell/IEEE/ACM/Elsevier/Springer/PLOS/NeurIPS/ICML/ICLR/CVPR/ACL/CHI 插图规范时使用。先建立 Figure Contract，再用 matplotlib/SVG 确定性生成可编辑矢量图，并运行合规审计。
argument-hint: "说明图要表达什么、数据/方法来源、目标期刊或会议；也可附 CSV、论文段落或现有图"
---

# 科研绘图工作流

目标不是“生成一张图”，而是交付一张**科学内容可核对、文字可编辑、尺寸/字体/色彩可投稿**的图。所有图先立契约，再绘制，再审计。

## 何时使用

- 用户要把实验结果、消融、训练曲线、分布、热图做成期刊图。
- 用户要根据方法/系统描述画流程图、pipeline、框架图、概念示意图、graphical abstract。
- 用户问“这张图符合 Nature/IEEE/CHI 要求吗”“为什么投稿系统说 DPI/字体不对”。
- 用户要求 SVG/PDF 可编辑、Illustrator 可改字、色盲友好、灰度可读。

## 不可协商的规则

1. **不伪造数据与结构**：图里的数值、误差线、显著性、节点和箭头必须来自用户数据、论文原文或用户确认的描述。缺失值保留为缺失，不补造。
2. **文字不交给生成式模型**：图内标签由代码写入。概念插图可用位图草稿启发构图，但带文字的正式图必须重绘为矢量文本。
3. **矢量优先**：线条、箭头、文字保持可编辑。PNG/TIFF 只用于照片、显微图或预览。
4. **先定终点**：没有明确目标期刊时，先问一次；仍不明确则用 `nature` 作为保守预设，并明确告知这是暂定。
5. **审计后才算完成**：生成后必须运行 `audit`，报告硬性违规与警告；不能只说“已生成”。
6. **不承诺录用**：合规检查不等于期刊接收，也不替代统计审稿和人工视觉复核。

## 工作流

### 第 1 步：收集最小输入

只问缺失且影响成图的信息：

- 目标期刊/会议（或“先按 Nature 单栏”）。
- 图要说明的一个核心结论（一句话）。
- 数据文件，或方法/系统描述，或现有参考图。
- 图型偏好（数据图 / 流程图 / 概念图 / 不确定）。
- 最终尺寸：单栏、1.5 栏、双栏；不确定就按预设默认。

能从上下文推断的就不要追问。用户已给的数据和术语优先于模板。

### 第 2 步：建立 Figure Contract

```bash
python <skill-dir>/scripts/scifig_cli.py plan \
  --type data|diagram|concept --venue <venue> --layout single|one_half|double \
  --id fig1 --title "<一句话结论>" --out <workdir>/fig1.contract.json
```

然后编辑契约。契约至少包含：

- `purpose`：这张图要让读者相信什么。
- `data` / `diagram`：数据列映射或节点/连线。
- `caption`：独立图注（不要画进图里）。
- `provenance`：数据来源、参考图、未确认项。
- `export`：`svg,pdf,png`（按需加 `tiff,drawio,mermaid`）。
- 总览+放大：`diagram.composition: "overview_detail"`，`diagram.groups[]`，每组 `detail.motifs[]` 与可选 `detail.arrows[]`。
- 主题：`style.theme: "rich" | "strict"`；rich 用于展示/图形摘要，strict 用于正式投稿。

完整字段与示例见 `references/figure-workflow.md` 和 `examples/contracts/`。

### 第 3 步：按图型路由

| 图型 | 判断标准 | 引擎 | 参考 |
| --- | --- | --- | --- |
| 数据图 | 有数值坐标轴、统计比较、分布、矩阵 | matplotlib + 期刊预设 | `references/chart-playbook.md` |
| 流程图 / 框架图 | 方框、箭头、阶段、分支、反馈 | 原生 SVG 分层布局 | `references/diagram-playbook.md` |
| 概念图 / graphical abstract | 中心概念、辐射关系、图标叙事 | SVG 径向/自由布局 + 线稿图标 | `references/diagram-playbook.md` |
| 总览 + 局部放大 | 顶部流程 + 彩色分组 + 底部详图 | `overview_detail` + 通用图元 | `references/diagram-playbook.md` |
| 照片/显微图合成 | 必须保留真实像素 | 位图 + 矢量标注分层 | `references/audit-checklist.md` |

**经验规则**：有数值轴 → 数据图；有方框和箭头 → 流程图；有隐喻和图标 → 概念图。不要用 AI 位图替代带精确标签的流程图。

### 第 4 步：生成

数据图：

```bash
python <skill-dir>/scripts/scifig_cli.py data \
  --spec <workdir>/fig1.contract.json \
  --data <workdir>/results.csv \
  --out <workdir>/fig1
```

流程图/概念图：

```bash
python <skill-dir>/scripts/scifig_cli.py diagram \
  --spec <workdir>/fig2.contract.json \
  --out <workdir>/fig2
```

生成规则：

- 画布物理尺寸直接取期刊栏宽；不要先画大再缩。
- 字号取期刊允许区间内的默认值；低于最小字号即失败。
- 默认 Okabe–Ito 色板；同时用线型/标记/填充纹理编码，颜色不是唯一通道。
- 不在图内写标题和图注；图注放契约与正文。
- 节点标签、坐标轴单位、上下标必须逐字核对。

### 第 5 步：合规审计

```bash
python <skill-dir>/scripts/scifig_cli.py audit \
  --file <workdir>/fig1.pdf --venue nature \
  --json <workdir>/fig1.audit.json
```

审计覆盖：

- 画布宽度/高度是否符合栏目；是否超过最大高度。
- 光栅有效 DPI（按最终尺寸换算），线图是否应改矢量。
- 最小/最大字号、字体白名单、字体是否嵌入、文字是否被转曲。
- RGB/CMYK、透明度、图片压缩。
- 色盲模拟与灰度下的颜色可分性。
- 内容是否贴边/可能裁切、留白是否异常。
- 文件格式、体积、页面数。

修复顺序：**尺寸 → 字体/字号 → 分辨率/格式 → 颜色可访问性 → 版面细节**。每次修改后重跑审计。

### 第 6 步：交付

交付时必须给出：

- 图文件路径（SVG/PDF/PNG，按需 TIFF）。
- 契约路径（可复现）。
- 审计摘要：错误数、警告数、未覆盖的人工检查项。
- 一句限制说明：哪些科学/统计问题需要作者确认。

推荐文件命名：

```text
figures/
├── fig1.contract.json
├── fig1.svg
├── fig1.pdf
├── fig1.png
├── fig1.audit.json
└── fig1.caption.txt
```

## CLI 速查

```bash
python <skill-dir>/scripts/scifig_cli.py doctor
python <skill-dir>/scripts/scifig_cli.py venues --venue ieee
python <skill-dir>/scripts/scifig_cli.py plan --type diagram --venue cell --out fig.contract.json
python <skill-dir>/scripts/scifig_cli.py data --spec fig.contract.json --data data.csv --out fig
python <skill-dir>/scripts/scifig_cli.py diagram --spec fig.contract.json --out fig
python <skill-dir>/scripts/scifig_cli.py audit --file fig.pdf --venue cell --json fig.audit.json
python <skill-dir>/scripts/scifig_cli.py convert --input fig.svg --out fig.pdf --width-mm 89
```

`<skill-dir>` 指本 SKILL.md 所在目录。所有命令都支持从任意工作目录运行。

## 期刊预设

预设文件：`assets/venue-presets.json`。当前覆盖：

`nature`、`science`、`cell`、`ieee`、`acm`、`chi`、`elsevier`、`springer`、`plos`、`neurips`、`icml`、`iclr`、`cvpr`、`acl`。

每个预设包含：栏宽、最大高度、字号区间、字体白名单、线宽、分辨率、格式、色彩模式、面板标签规则、来源链接与置信度。详细对照见 `references/venue-requirements.md`。

**投稿前必须复核官网**：出版社规范会变；标为 `cross-checked` 的条目只经过权威二手源交叉核对。

## 质量门

完成前逐项确认：

- [ ] 图能独立回答 `purpose`，不依赖正文猜测。
- [ ] 所有数值与原文/数据一致；缺失值未被补造。
- [ ] 坐标轴有单位，刻度线存在，无背景网格（除非期刊允许）。
- [ ] 字号 ≥ 期刊最小值；面板标签大小写与字重符合预设。
- [ ] 颜色在灰度与三类色盲模拟下仍可区分，且有非颜色编码。
- [ ] 文字是文本对象，未被转曲；PDF 字体已嵌入。
- [ ] 画布宽度等于目标栏宽，未被二次缩放。
- [ ] 图注在正文/契约中，不在图内。
- [ ] 总览+放大图的面板字母、分组色、放大连接清晰；rich/strict 选择符合用途。
- [ ] 审计报告已附，未覆盖项已列明。

## 限制

- 自动布局不处理任意复杂交叉连线；需要时导出 Draw.io 继续编辑。
- 审计不能判断统计方法是否正确、图像是否被不当处理。
- Science/Cell 官方页面反爬时，预设数字为交叉核对值，必须复核。
- 未安装 Illustrator 时，Illustrator 技能只做环境检查，不声称已导出 `.ai`。

## 参考

- `references/venue-requirements.md`：期刊/会议硬指标与来源。
- `references/figure-workflow.md`：Figure Contract 字段、示例与常见返工。
- `references/chart-playbook.md`：图型选择、统计标注、可访问性。
- `references/diagram-playbook.md`：流程图/概念图布局、箭头语义、图标。
- `references/audit-checklist.md`：自动审计之外的人工检查。
- `references/open-source-stack.md`：调研到的开源生态、许可证与采用边界。
