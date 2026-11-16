---
name: sci-figure-audit
description: 对已有科研图做投稿合规审计与修复。用户问“这张图符合 Nature/Science/Cell/IEEE/ACM/CHI 要求吗”“为什么投稿系统提示 DPI/字体/尺寸不对”“检查 PDF/SVG/PNG 的字体嵌入、色盲和灰度可读性”时使用。输出逐项 finding、严重度、修复建议和复检命令。
argument-hint: "给出图文件路径与目标期刊/会议；可附加期望栏宽（single/1.5/double）"
---

# 科研图合规审计

审计是“找硬伤 + 给可执行修复”，不是打分表演。先跑机器检查，再做人工清单，最后按严重度修复并复检。

## 快速使用

```bash
python <plugin-dir>/skills/sci-figure-audit/scripts/audit_cli.py \
  --file path/to/figure.pdf --venue nature \
  --json path/to/figure.audit.json
```

或直接调用主技能脚本（同一实现）：

```bash
python <plugin-dir>/skills/sci-figure/scripts/scifig_cli.py audit \
  --file figure.svg --venue ieee --layout double
```

参数：

- `--venue`：`nature|science|cell|ieee|acm|chi|elsevier|springer|plos|neurips|icml|iclr|cvpr|acl`。
- `--layout`：可选，`single|one_half|double`；用于核对目标栏宽。
- `--width-mm`：可选，手工指定目标宽度；优先于预设。
- `--theme rich|strict`：主题。rich 在 Nature/IEEE/Cell/ACM 等正式规范下会给出“建议切 strict”的 warning。
- `--strict`：有 error 时退出码为 1，便于 CI。
- `--json`：写出机器可读报告。

## 审计维度

| 维度 | 机器检查 | 常见修复 |
| --- | --- | --- |
| 画布 | PDF 页面/位图换算宽度、最大高度 | 按栏宽重排，不要缩放整图 |
| 分辨率 | 位图有效 DPI、矢量/光栅比例 | 重新导出高分辨率，或改矢量 |
| 字体 | 字族白名单、最小/最大字号、是否嵌入 | 改 Arial/Helvetica/Times，嵌入 TrueType 2/42 |
| 文字 | 是否存在文本对象、是否疑似转曲 | 保留可编辑文本，不 outline |
| 色彩 | RGB/CMYK、透明度、主色数量 | 转 RGB，压平透明，减少装饰色 |
| 可访问性 | 色盲模拟、灰度亮度差 | 改 Okabe–Ito/Tol 色板，加线型/标记 |
| 版面 | 内容贴边、留白、页面数 | 增加边距，去掉无关面板 |
| 主题 | rich 的装饰性色带/半透明 | 正式投稿切 strict，或确认期刊接受 |
| 格式 | 扩展名与期刊接受格式 | 导出 PDF/EPS/TIFF 等 |

## 严重度

- **error**：明确违反硬性规范，投稿系统可能直接拒收。必须修。
- **warning**：不理想或高风险，可能被生产编辑退回。建议修。
- **info**：优化项，不影响技术合规。

## 修复循环

1. 先读 JSON 的 `findings`，按 `severity` 排序。
2. 只改一类问题，重新生成/导出，再复检；不要一次改十处导致无法归因。
3. 尺寸类问题优先于颜色类问题；字体嵌入优先于字体美观。
4. 修完后对比 `summary.score` 与 error 数，确认确实改善。
5. 仍无法自动修的（如统计标注、图像完整性、面板逻辑），写入人工清单交给作者。

## 机器不能替代的检查

- 数据是否真实、误差线定义是否写清、显著性检验是否匹配。
- 显微图/凝胶/流式的图像完整性（拼接、对比度、重复使用）。
- 面板顺序是否服务论证，图注是否自足。
- 颜色语义是否一致（同一方法在全文中是否同色）。
- 文字是否与正文术语一致（大小写、缩写、单位）。
- 是否存在版权/许可问题（第三方图标、参考图、字体）。

## 报告模板

```markdown
### 合规审计：<file> → <venue>
- 结论：<通过 / 有 N 个硬性违规 / 无法判定>
- 画布：<宽×高>，目标 <栏宽>，<符合/偏大/偏小>
- 分辨率：<有效 DPI>，要求 <min>
- 字体：<字族>，最小 <pt>，<已嵌入/未嵌入/疑似转曲>
- 色彩：<RGB/CMYK>，色盲最小色差 ΔE=<n>，灰度最小亮度差=<n>
- 硬性违规：<逐条>
- 警告：<逐条>
- 建议修复顺序：<1…n>
- 人工复核：<统计/完整性/版权/图注>
```

## 边界

- 审计基于文件元数据、页面对象和启发式版面分析；不能保证与期刊生产系统完全一致。
- Science/Cell 等官网受限时，预设值来自交叉核对，投稿前仍需核对官网。
- 不伪造“已通过投稿系统”的结论；只报告本工具实际检查到的内容。
