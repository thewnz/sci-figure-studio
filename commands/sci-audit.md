---
description: 检查已有科研图是否符合目标期刊/会议的尺寸、DPI、字体、嵌入、色彩与可访问性要求
argument-hint: "给出图文件路径（PDF/SVG/PNG/TIFF）与目标期刊/会议"
---

# /sci-audit

使用 `sci-figure-audit` 技能。

## 步骤

1. 确认文件路径、格式、目标期刊/会议、期望栏宽（可选）。
2. 运行：

```bash
python <PLUGIN_ROOT>/skills/sci-figure-audit/scripts/audit_cli.py \
  --file <figure> --venue <venue> --json <figure>.audit.json
```

3. 按严重度解释 findings：
   - **error**：硬性违规，必须修。
   - **warning**：高风险，建议修。
   - **info**：优化项。
4. 给出修复顺序：尺寸 → 字体/字号 → 分辨率/格式 → 色彩可访问性 → 版面。
5. 列出机器无法判断的人工项：数据真实性、统计口径、图像完整性、图注自足、版权。
6. 修复后重跑，确认 error 数与分数确实改善。

## 输出

```markdown
### 合规审计：<file> → <venue>
- 结论：<通过 / N 个硬性违规 / 无法判定>
- 画布：...
- 分辨率：...
- 字体：...
- 色彩：...
- 硬性违规：...
- 警告：...
- 修复顺序：...
- 人工复核：...
```

不得声称“已通过期刊投稿系统”；只报告本工具实际检查到的结果。
