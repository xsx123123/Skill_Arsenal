---
name: ggplot-polisher
description: 将 R ggplot2 图表打磨为 Cell/Nature/Science 等顶刊发表级水准的完整工作流。覆盖：科研配色选择（个人精选色板、ggsci 期刊色板、连续/发散渐变）、多图排版拼图（patchwork、aplot 轴对齐、ggpubr 统计标注与出版级主题）、顶刊 Figure 排版规律与图注规范、出版级导出。当用户要求美化/润色/排版/拼图 ggplot2 图、绘制发表级科研 Figure、做统计显著性标注，或提到 patchwork、aplot、ggsci、ggpubr、tidyplots 配色时使用。仅适用于 R 语言的 ggplot2 绘图生态，不处理 Python matplotlib/seaborn。
---

# ggplot2 顶刊发表级绘图打磨

## 工作流

按顺序执行，每一步都服务于"发表级"目标：

1. **明确图形角色**：这张是单图还是多 panel Figure？是示意图 + 数据的组合，还是纯数据面板？
2. **选配色**：按 `references/colors.md` §1 的决策表选色板；同一张 Figure 的所有 panel 共用同一套分组颜色（把色板向量存为变量全局复用）。
3. **打磨单图**：主题统一（默认 `theme_pubr()` / `theme_pubclean()`，见 `references/ggpubr.md` §1）；需要统计检验时加 `stat_compare_means()`。
4. **拼图排版**：按下方"拼图工具路由"选包。
5. **加标签与图注**：panel 用 A/B/C tag（patchwork `plot_annotation(tag_levels = "a")` 或 ggpubr `ggarrange(labels = ...)`）；图注按"图注规范"写。
6. **出版级导出**：`ggsave()`，`dpi = 600`（1000 dpi 文件过大，600 通常足够），宽度按期刊单栏（~85 mm）/双栏（~180 mm）设定；热图等大图高度适当放大。

## 顶刊 Figure 排版规律（源自 Cell 论文 Figure 拆解）

1. **一张 Figure 只讲一个核心结论**。图注首句是一句加粗的结论式标题句（如"VIDA mRNA-LNP 具有良好的体内外安全性"），不是"某某结果图"。
2. **Panel 排序：先示意图/流程图，后数据面板**。Figure 开头的 panel (A) 通常是机制示意图或实验流程图，常占满整行宽度；后续 (B–E) 等才是柱状图、流式、热图等数据面板。
3. **规则网格，2–3 列为主**。子图按网格对齐排列；数据面板大小相近时等宽分布。"左列数据 + 右侧大图/示意图"的非对称版式用 patchwork 的 `design` 实现。
4. **Panel 标签**：(A)、(B–E) 这种期刊式标签；同一逻辑组的多个 panel 在图注里合并描述（"(C–D) LDH 释放及细胞内 ATP 水平检测"）。
5. **组图数量克制**：一篇研究正文 Figure 通常 5–7 张，每张 3–10 个 panel。
6. **视觉统一**：所有 panel 主题、字体、线宽一致；分组颜色全局一致；示意图风格与数据图协调（配色可呼应 `references/colors.md` 的色板）。

## 图注规范

结构 = 加粗结论标题句 + 分段 panel 描述：

```
**一句话结论（加粗，陈述发现而非"结果展示"）**

**(A)** 第一个 panel 做了什么。
**(B–E)** 逻辑相关的一组 panel 合并描述：实验内容 + 检测指标。
**(F)** ...
```

## 拼图工具路由

| 场景 | 工具 | 参考 |
|---|---|---|
| 通用多图排版（默认，用户首选） | **patchwork** | `references/patchwork.md` |
| 坐标轴必须严格对齐：热图+聚类树、热图+注释条 | **aplot**（`insert_left/top/right`） | `references/aplot.md` |
| 一键 P 值标注、出版级主题、图旁嵌表格 | **ggpubr** | `references/ggpubr.md` |
| 不规则自由版式 | patchwork `design` 字符串（优于括号嵌套，嵌套会导致边框不对齐） | `references/patchwork.md` §6 |

快速决策：
- 只是"把几张图摆整齐" → patchwork：`(p1 | p2) / (p3 + p4) + plot_layout(guides = 'collect')`
- 子图与主图共享坐标（聚类树对齐热图行列） → aplot
- 箱线图/小提琴图加显著性星号 → ggpubr `stat_compare_means()`
- 多 panel 需要统一改主题/删图例 → patchwork `&` 操作符

## 配色

所有色板的 hex 值、ggsci 期刊色板速查表、渐变生成技巧、色盲检查，见 `references/colors.md`。色板预览图在 `assets/palettes/`，选色时可查看。要点：

- 分类数据日常首选 `spring1`；正式投稿按目标期刊选 ggsci（NPG/AAAS/NEJM/Lancet/JAMA/Frontiers）。
- 连续数据用 `bluepinkyellow` / `rocket` / `turbo`；Z-score 类发散数据用 `scale_fill_gradient2(low, mid = "#ffffff", high)`。
- 分组很多（>10）用 `my36colors` / `my55colors` / `color3list`。
- 用 `color_visibility()`（`references/colors.md` §7）预览任何色板向量。

## 导出与交付

- 统一用 `ggsave()`：`dpi = 600`，`width` / `height` 显式指定（英寸），大图（如千基因热图）高度给到 8 左右。
- ggpubr 用户可用 `ggexport(filename, width, height, res = 600)`。
- 输出 PNG 用于预览；期刊投稿同时提供 PDF（矢量）版本。
- 拼图里的每张单图尽量先各自调好再拼，避免拼完再逐子图返工。
