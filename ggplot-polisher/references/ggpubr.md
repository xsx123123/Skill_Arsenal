# ggpubr 出版级绘图与统计标注指南

> ggpubr（ggplot2 Based Publication Ready Plots）：填补"数据探索"与"期刊投稿"之间的最后一步。核心：`stat_compare_means()` 一键 P 值标注、出版级主题、图表与表格融合。
> 安装：`install.packages("ggpubr")`
> 注意：拼图功能用户更喜欢用 patchwork，ggpubr 的价值主要在**统计标注、主题、表格**。

## 目录

- [1 出版级主题](#1-出版级主题)
- [2 统计显著性标注](#2-统计显著性标注)
- [3 拼图与布局](#3-拼图与布局)
- [4 图表与表格融合](#4-图表与表格融合)

## 1 出版级主题

| 主题 | 特征 | 适用 |
|---|---|---|
| `theme_pubr()` | 白底、黑色 XY 轴实线、无网格 | 绝大多数期刊标准统计图 |
| `theme_pubclean()` | 白底、无坐标轴实线、淡网格 | 散点图 / 强调数据分布、减少线条干扰 |
| `theme_classic2()` | `theme_classic()` 优化版，facet 时坐标轴线也正确闭合 | 分面图 |
| `labs_pubr()` | 一键把标题、轴标签改为粗体大号出版风格 | 自定义背景时叠加 |

配套简化绘图函数：`ggboxplot()`、`ggdotplot()`、`ggscatter()` 等，`ggplot()` 的统计直觉化封装。

## 2 统计显著性标注

`stat_compare_means()`：在箱线图 / 小提琴图上自动进行 t 检验、Wilcoxon、ANOVA 等，绘制显著性连线和星号，无需手动计算坐标。可叠加两次：一次画两两比较连线，一次画全局检验。

```r
library(ggpubr)
data("ToothGrowth")

ggboxplot(ToothGrowth, x = "dose", y = "len", add = "jitter") +
  # 两两比较
  stat_compare_means(comparisons = list(c("0.5", "1"), c("1", "2")),
                     method = "t.test") +
  # 全局检验（如 ANOVA）
  stat_compare_means(label.y = 50, vjust = 0.5,
                     label.sep = '\n', label.x = 2, hjust = 0.5) +
  theme_pubclean() +
  theme(legend.position = 'bottom')
```

常用参数：`method`（"t.test" / "wilcox.test" / "anova" / "kruskal.test"）、`comparisons`（成对比较列表）、`label.y` / `label.x`（标注位置）、`paired`（配对检验）。

## 3 拼图与布局

`ggarrange()`：多图排列，自动添加 A/B/C 标签，可共享同一图例（`common.legend = TRUE` 是杀手锏）。与 `ggexport()` 配合导出。

```r
p1 <- ggboxplot(ToothGrowth, x = "dose", y = "len", fill = "dose") + theme_pubclean()
p2 <- ggdotplot(ToothGrowth, x = "dose", y = "len", fill = "dose") + theme_classic2()

ggarrange(p1, p2, ncol = 2, nrow = 1,
          labels = c("A", "B"),
          common.legend = TRUE, legend = "bottom") |>
  ggexport(filename = "ggpubr_ggarrange.png", width = 3000,
           height = 1500, res = 600)
```

`annotate_figure()`：给拼好的整张大图加总标题、图编号（fig.lab）、来源说明。

```r
ggarrange(p1, p2, ncol = 2, labels = c("A", "B"),
          common.legend = TRUE, legend = "bottom") |>
  annotate_figure(top = '总标题',
                  fig.lab = 'Figure 1',
                  fig.lab.size = 12, fig.lab.face = "bold") |>
  ggexport(filename = "final.png", width = 3000, height = 1500, res = 600)
```

`get_legend()` + `as_ggplot()`：从现有图中"扣"下图例并转为 ggplot 对象——做特殊布局（图例单独占一格）时非常有用。

## 4 图表与表格融合

`ggtexttable()`：把数据框直接画成精美文本表格，与主图并列拼版（可配合 patchwork 的 `plot_layout(widths = c(1.5, 1))`）。

```r
df <- head(iris[, 1:3], 10)
table_plot <- ggtexttable(df, rows = NULL, theme = ttheme("mOrange"))

scatter_plot <- ggplot(iris, aes(Sepal.Length, Sepal.Width, color = Species)) +
  geom_point(size = 3, alpha = 0.7) +
  scale_color_brewer(palette = "Set2") +
  theme_pubclean() +
  theme(legend.position = "bottom")

final_plot <- scatter_plot + table_plot + plot_layout(widths = c(1.5, 1))
```
