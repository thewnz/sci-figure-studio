# Sci-Figure Studio · 科研绘图工作台

面向顶刊顶会投稿的确定性科研绘图 Qoder 插件。数据图、流程图、概念图、总览+局部放大、期刊合规审计。

## 快速开始

```bash
git clone https://github.com/thewnz/sci-figure-studio.git
cd sci-figure-studio
pip install -r requirements.txt
python scripts/package.py
```

在 Qoder 扩展面板导入 `dist/sci-figure-studio-0.2.1.zip`。

## 命令

```text
/sci-figure-studio:sci-figure
/sci-figure-studio:sci-data
/sci-figure-studio:sci-flow
/sci-figure-studio:sci-audit
```

## CLI

```bash
python skills/sci-figure/scripts/scifig_cli.py venues --venue nature
python skills/sci-figure/scripts/scifig_cli.py data --spec examples/contracts/nature_data.json --data examples/data/example_results.csv --out outputs/fig1
python skills/sci-figure/scripts/scifig_cli.py diagram --spec examples/contracts/nature_rich_overview.json --out outputs/overview
python skills/sci-figure/scripts/scifig_cli.py audit --file outputs/fig1.pdf --venue nature
```

## 特性

- 14 套期刊/会议预设：Nature、Science、Cell、IEEE、ACM/CHI、Elsevier、Springer、PLOS、NeurIPS、ICML、ICLR、CVPR、ACL
- 数据图：折线、分组柱、散点、热图、箱线/小提琴、直方图
- 流程图/概念图：分层、径向、时间线、矩阵、自由布局
- 总览+局部放大：彩色分组、放大面板、光谱/张量/网络/矩阵/公式图元
- rich/strict 双主题：rich 用于展示，strict 用于正式投稿
- 合规审计：尺寸、DPI、字号、字体嵌入、色盲/灰度、贴边

## 结构

```text
skills/sci-figure/     主工作流 + CLI + 期刊预设
skills/sci-figure-audit/ 审计
skills/sci-figure-illustrator/ 可选 Illustrator 交接
commands/             四个命令
examples/             示例数据与契约
tests/smoke_test.py   冒烟测试
```

## 已知限制

- rich 的装饰性色彩在严格期刊审计下会提示切 strict
- 通用图元是抽象符号，不生成分子结构或真实实验图像
- Science/Cell 预设为交叉核对值，投稿前请复核官网
- 审计不能替代统计审稿与人工视觉复核

## 许可

MIT © thewnz
