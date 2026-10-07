# patchwork 拼图指南

> 用户首选的拼图包。目标：用最简单的语法把多个 ggplot 拼成同一张图。
> 安装：`install.packages('patchwork')` 或 `devtools::install_github("thomasp85/patchwork")`

## 目录

- [1 基础语法](#1-基础语法)
- [2 布局控制](#2-布局控制)
- [3 注释与标签](#3-注释与标签)
- [4 整体修改 &](#4-整体修改-)
- [5 跨页对齐](#5-跨页对齐)
- [6 design 自由布局](#6-design-自由布局)

## 1 基础语法

```r
library(ggplot2)
library(patchwork)

p1 <- ggplot(mtcars) + geom_point(aes(mpg, disp)) + ggtitle('Plot 1')
p2 <- ggplot(mtcars) + geom_boxplot(aes(gear, disp, group = gear)) + ggtitle('Plot 2')
p3 <- ggplot(mtcars) + geom_point(aes(hp, wt, colour = mpg)) + ggtitle('Plot 3')
p4 <- ggplot(mtcars) + geom_bar(aes(gear)) + facet_wrap(~cyl) + ggtitle('Plot 4')

all <- (p1 + p2)            # + 按行拼接
all <- p1 | p2              # | 并排
all <- (p1 | p2) / (p3 | p4) # / 堆叠；| 和 / 可组合
```

- 在任意子图后直接加 ggplot2 元素（如 `+ theme_light()`）只作用于该子图：
  ```r
  all_1 <- p1 + theme_light() + p2   # 只改 p1
  all_2 <- p1 + p2 + theme_light()   # 只改 p2
  all_3 <- p1 + theme_light() + p2 + theme_light()  # 都改
  ```
- 非 ggplot 对象（如 `gridExtra::tableGrob()` 表格）也能与 ggplot 混拼。
- `plot_spacer()` 插入空白占位：`p1 | plot_spacer() | p2`

## 2 布局控制

默认按行填充并保持网格接近正方形。用 `plot_layout()` 精细控制：

```r
# 行列数与填充方向
p1 + p2 + p3 + p4 + plot_layout(nrow = 3, byrow = FALSE)

# 子图宽 / 高比例
p1 + p2 + p3 + p4 + plot_layout(widths = c(2, 1),
                                heights = unit(c(5, 1), c('cm', 'null')))

# 合并多个子图的图例为一个（杀手锏）
p1 + p2 + p3 + p4 + plot_layout(guides = 'collect')

# 相同坐标范围的子图共用一套坐标轴
p1 + p2 + plot_layout(axes = "collect")
```

## 3 注释与标签

```r
# 总标题
all + plot_annotation(title = "Mtcar test plot")

# 子图标签：tag_levels = "a" 小写 / "A" 大写 / "I" 罗马数字
all + plot_annotation(tag_levels = "a")
all + plot_annotation(tag_levels = "A")
all + plot_annotation(tag_levels = "I")

# 多级标签 + 前后缀（科研 Figure 常用）
p1 + p2 + p3 + p4 + plot_annotation(tag_levels = c('A', '1'),
                                    tag_prefix = 'Fig. ',
                                    tag_sep = '.', tag_suffix = ':')

# 图中插图（小图嵌入大图，坐标为相对位置 0-1）
p1 + inset_element(p2, left = 0.5, bottom = 0.5, right = 0.99, top = 0.99)
```

## 4 整体修改 `&`

`&` 把元素加到 patchwork 中**所有**子图；`*` 只加到当前嵌套层级的所有子图。

```r
# 删除所有子图图例（配合 guides collect 常用）
p + plot_layout(guides = 'collect') & theme(legend.position = 'none')

# 统一修改图例外观
p + plot_layout(guides = "collect") & labs(color = 'Cell Type') &
  guides(color = guide_legend(keywidth = 1, keyheight = 1.5, ncol = 2,
                              override.aes = list(size = 4)))
```

## 5 跨页对齐

不同图的元素不同会导致图主体宽度不一致，跨文件排版时先对齐尺寸：

```r
p1 <- p1 + theme_classic()
p3 <- p3 + theme_light()
p3_dims <- get_dim(p3)
p1_aligned <- set_dim(p1, p3_dims)   # 让 p1 的绘图区尺寸与 p3 一致
```

## 6 design 自由布局

不规则排版用字符串 `design` 定义画布（注意：用括号嵌套如 `p1 / (p2 | p3)` 会导致不同区域子图边框不对齐，`design` 能让整个画布在同一坐标系下对齐）。字母对应子图顺序（第 1 个字母 = p1，以此类推）：

```r
design <- "
  AAAADDDDD
  BBBBDDDDD
  CCCCDDDDD
"
all <- p1 + p2 + p3 + p4 + plot_layout(design = design) +
  plot_annotation(tag_levels = 'a')
```

此例：p1 占左上横条，p2/p3 堆叠在左下，p4 占满右侧整列——顶刊 Figure 中"左侧数据面板 + 右侧大图 / 示意图"的经典版式。
