# aplot 轴对齐拼图指南

> 用途与 patchwork 不同：aplot 解决的是**相互关联的子图之间坐标轴精确对齐**的问题，典型场景：热图 + 层级聚类树、热图 + 注释条。它能把副图插入主图的上 / 下 / 左 / 右四个方向，并自动重排主图坐标以严格对齐。
> 仓库：https://github.com/YuLab-SMU/aplot

## 目录

- [1 三个核心函数](#1-三个核心函数)
- [2 完整示例：热图 + ggtree 聚类树](#2-完整示例热图--ggtree-聚类树)

## 1 三个核心函数

```r
library(aplot)
p |> insert_left(gene_tree, width = 0.2)    # 插入左侧，共享 / 对齐 Y 轴
p |> insert_top(sample_tree, height = 0.1)  # 插入顶部，对齐 X 轴
p |> insert_right(tree, width = 0.2)        # 插入右侧，对齐 Y 轴
```

- `width` / `height` 控制副图与主图的相对比例，避免副图喧宾夺主。
- `insert_left` / `insert_top` 会**根据树结构自动重排热图**的行列顺序，无需手动对齐。
- 可用管道 `|>` 连续插入多个方向。

## 2 完整示例：热图 + ggtree 聚类树

```r
library(ggplot2)
library(data.table)
library(tidyr)
library(dplyr)
library(ggtree)
library(aplot)

# 1. 读数据，筛选高表达基因（行均值 top 1000）
gene_matrix_data <- fread("gene_matrix.csv") |>
  tibble::column_to_rownames(var = "transcript_id") |>
  dplyr::select(-c("subject_id", "Entry Name")) |>
  mutate(avg_exp = rowMeans(pick(everything()))) |>
  slice_max(order_by = avg_exp, n = 1000) |>
  select(-avg_exp)

# 2. 行（基因）Z-score 标准化；scale() 默认对列操作，需两次转置
gene_matrix_data <- t(scale(t(gene_matrix_data)))

# 3. 转长格式
plot_data <- gene_matrix_data |>
  as.data.frame() |>
  rownames_to_column(var = "gene") |>
  pivot_longer(cols = matches("^(CK|T10|T20)"),
               names_to = "cell", values_to = "value")

# 4. 主热图：Z-score 中点为 0，gradient2 的 midpoint 默认 0 无需指定
p <- ggplot(plot_data, aes(x = cell, y = gene, fill = value)) +
  geom_tile() +
  scale_fill_gradient2(low = "#303cf9", mid = "#ffffff", high = "#fe5357") +
  scale_y_discrete(position = "right") +
  labs(fill = 'Gene Expression\n(Z-score)') +
  theme_minimal() +
  theme(axis.text.x = element_blank(),
        axis.text.y = element_blank(),   # 1000 个基因，隐藏 Y 轴标签防重叠
        axis.ticks = element_blank(),
        panel.grid = element_blank(),
        legend.title = element_text(size = 8, hjust = 0.5),
        legend.title.position = "top",
        legend.text = element_text(size = 7),
        legend.key.width = unit(0.5, "cm"),
        legend.key.height = unit(0.4, "cm")) +
  xlab(NULL) + ylab(NULL)

# 5. 聚类
gene_dist   <- dist(gene_matrix_data) |> hclust()
sample_dist <- dist(t(gene_matrix_data)) |> hclust()

# 6. 聚类树；顶部树必须加 layout_dendrogram()
gene_tree <- ggtree(gene_dist, branch.length = 'none', size = 0.4,
                    linetype = 1, alpha = 0.8, color = "black")
sample_tree <- ggtree(sample_dist, branch.length = 'none', size = 0.4,
                      linetype = 1, alpha = 0.8, color = "black") +
  layout_dendrogram()

# 7. 拼图：aplot 自动按树结构重排热图行列
plot <- p |>
  insert_left(gene_tree, width = 0.2) |>
  insert_top(sample_tree, height = 0.1)

# 8. 保存：1000 基因建议高度 8 左右；600 dpi 通常够出版级
ggsave('aplot_heatmap_tree.png', width = 6, height = 8, dpi = 600, plot = plot)
```

同样的思路可用于：热图上方插入样本分组注释条、主图侧边插入密度图 / 柱状注释条等任何需要坐标严格对齐的组合。
