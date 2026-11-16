---
description: 根据方法/系统描述画可编辑的论文流程图、框架图、概念图或 graphical abstract
argument-hint: "粘贴方法段落或列出步骤/模块/连线；说明目标期刊与横/竖版式"
---

# /sci-flow

使用 `sci-figure` 技能的流程图/概念图分支。

## 步骤

1. 从文本提取：阶段、节点、输入输出、分支/汇合、反馈、条件标签。节点数 > 8 时先分组。
2. 选择布局：
   - 顺序 pipeline → `layered` + horizontal。
   - 分层架构 → `layered` + vertical。
   - 中心概念 → `radial`。
   - 时间线 → `timeline`。
   - 矩阵/特征筛选 → `matrix`。
   - 参考图复刻 → `freeform`（归一化坐标）。
   - 总览 + 局部放大 → `composition: "overview_detail"`，用 `groups` 组织 2–4 个彩色面板，`detail.motifs` 放光谱/张量/网络/公式等图元。
3. 写 Figure Contract 的 `diagram.nodes` 与 `diagram.edges`：
   - 每个节点有稳定 `id`、精确 `label`、`stage`、`kind`、可选 `icon`。
   - 每条边声明 `from`、`to`、`style`、`arrow`；条件标签只写原文有依据的词。
4. 运行：

```bash
python <PLUGIN_ROOT>/skills/sci-figure/scripts/scifig_cli.py diagram \
  --spec <workdir>/fig.contract.json --out <workdir>/fig
```

5. 需要继续编辑时导出 `drawio`；需要文本图源时导出 `mermaid`。
6. 运行 `audit`；检查文字是否溢出、箭头是否穿过非端点节点、反馈线是否绕行。rich 在正式期刊规范下会提示切 strict。
7. 交付 SVG/PDF（可编辑）+ PNG（预览）+ 契约 + 审计摘要。

## 禁止

- 不用 AI 位图生成带精确术语的流程图。
- 不添加原文没有的模块、箭头或因果。
- 不用颜色作为唯一分组编码；同时用边框/编号/线型。
- 不让箭头穿过无关节点；复杂交叉留给 Draw.io 手工调整。
