---
description: 把 CSV/表格/实验结果做成符合期刊规范的数据图（折线、柱状、散点、热图、箱线/小提琴、直方图）
argument-hint: "附数据文件或表格，说明 x/y、分组、误差线、目标期刊与图型"
---

# /sci-data

使用 `sci-figure` 技能的数据图分支。

## 步骤

1. 读取数据，确认列名、单位、分组、缺失值与重复测量结构。
2. 选择图型：
   - 时间/步数趋势 → 折线（带误差带/误差线）。
   - 多方法×多基准 → 分组柱状。
   - 单指标排名 → 水平柱状。
   - 两连续变量 → 散点。
   - 矩阵 → 热图。
   - 分布 → 箱线/小提琴/直方图。
3. 建立/编辑 Figure Contract：`chart.kind`、`x`、`y`、`group`、`yerr`、`xlabel`、`ylabel`、`highlight`、`caption`。
4. 运行：

```bash
python <PLUGIN_ROOT>/skills/sci-figure/scripts/scifig_cli.py data \
  --spec <workdir>/fig.contract.json --data <workdir>/data.csv --out <workdir>/fig
```

5. 运行 `audit`，确认字号、DPI、宽度、字体嵌入、色盲与灰度。
6. 报告数据映射和统计口径；缺失值/误差线定义必须写清。

## 禁止

- 不补造缺失值，不把标准差当标准误。
- 不在没有检验依据时加显著性星号。
- 不用饼图表达多类比例（期刊图优先堆叠条/点图）。
- 不把标题和图注画进图内。
