---
description: 从需求、数据或方法描述出发，生成符合目标期刊/会议规范的科研图
argument-hint: "图要表达什么、数据或方法来源、目标期刊/会议（可附 CSV 或现有图）"
---

# /sci-figure

使用 `sci-figure` 技能完成一张可投稿的科研图。严格按“契约 → 生成 → 审计 → 修复 → 交付”执行。

## 步骤

1. 读取用户输入；只补问影响成图的关键缺口：目标期刊/会议、核心结论、数据/方法来源、图型、栏宽。
2. 运行插件目录下的 `skills/sci-figure/scripts/scifig_cli.py plan` 建立 Figure Contract，并补齐 `purpose`、`data`/`diagram`、`caption`、`provenance`。
3. 按图型路由：
   - 有数值轴 → `data` 子命令。
   - 方框/箭头/阶段 → `diagram` 子命令。
   - 中心概念/图标叙事 → `diagram` 子命令（radial/freeform + icon）。
   - 参考图式总览 + 局部放大 → `diagram` 子命令的 `overview_detail` 构图，rich/strict 双主题。
4. 生成 SVG/PDF/PNG（按需 TIFF/Draw.io/Mermaid），不要用生成式模型直接出带字图。
5. 运行 `audit`，按 error → warning 修复并复检。
6. 交付：图文件、契约、审计摘要、未覆盖的人工检查项。

## 必须遵守

- 不伪造数据、节点、箭头或显著性。
- 图内标签逐字核对；图注不画进图。
- 颜色不是唯一编码；默认 Okabe–Ito 色板。
- 没有审计报告，不得声称“已完成”。
- 不承诺期刊录用。

## 输出

```text
figures/
├── <id>.contract.json
├── <id>.svg
├── <id>.pdf
├── <id>.png
└── <id>.audit.json
```
