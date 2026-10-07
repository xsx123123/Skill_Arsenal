# 科研配色速查（R / ggplot2）

## 目录

- [1 选色决策](#1-选色决策)
- [2 个人精选离散色板](#2-个人精选离散色板)
- [3 连续 / 发散渐变色板](#3-连续--发散渐变色板)
- [4 ggsci 期刊色板](#4-ggsci-期刊色板)
- [5 系统色板](#5-系统色板)
- [6 动态生成颜色](#6-动态生成颜色)
- [7 颜色可视化工具](#7-颜色可视化工具)

预览图位于 `assets/palettes/`，选色时可查看。

## 1 选色决策

| 数据类型 | 选择 | 推荐 |
|---|---|---|
| 分类数据（几组到 ~10 组） | 个人精选离散色板，见 §2 | 首选 `spring1`（用户标记 recommend）；正式投稿按期刊选 ggsci，见 §4 |
| 分类数据（>10 组） | `my36colors` / `my55colors` / `color3list` | 分组多时按色相循环 |
| 连续数据（表达量、强度） | 渐变色板，见 §3 | `bluepinkyellow`、`rocket`、`turbo` |
| 正负差异（Z-score、logFC） | `scale_fill_gradient2()` 三色发散 | 例：`low = "#303cf9", mid = "#ffffff", high = "#fe5357"` |
| 目标期刊有品牌色要求 | ggsci 对应期刊色板 | NPG / AAAS / NEJM / Lancet / JAMA / BMJ / JCO / Frontiers |

原则：颜色数量宁少勿多；同一张 Figure 内所有子图共用同一套分组颜色（把色板向量存成变量全局复用）；避免红绿组合（色盲不友好），必要时用 `dichromat` 包检查。

## 2 个人精选离散色板

### spring1（推荐，日常首选）

```r
spring1 <- c("#f6bcfd","#8dd3c6","#ffc512","#ffa300","#ff7d00","#ff6581","#f8d90d",
             "#a5da6b","#e578d6","#ffd2d8","#90e4cd","#84dce0","#fe65b3")
```

预览：`assets/palettes/spring1.png`

### my36colors（36 组以内）

```r
my36colors <- c('#E5D2DD', '#53A85F', '#F1BB72', '#F3B1A0', '#D6E7A3', '#57C3F3', '#476D87',
                '#E95C59', '#E59CC4', '#AB3282', '#23452F', '#BD956A', '#8C549C', '#585658',
                '#9FA3A8', '#E0D4CA', '#5F3D69', '#C5DEBA', '#58A4C3', '#E4C755', '#F7F398',
                '#AA9A59', '#E63863', '#E39A35', '#C1E6F3', '#6778AE', '#91D0BE', '#B53E2B',
                '#712820', '#DCC1DD', '#CCE0F5', '#CCC9E6', '#625D9E', '#68A180', '#3A6963',
                '#968175')
```

预览：`assets/palettes/my36colors.png`

### my55colors（55 组，色相循环）

```r
my55colors <- c("#de452f","#004080","#8c0043","#00994d","#69306d","#00a67c",
                "#8e7700","#00b3b3","#9b3500","#005799","#bf6e00","#003366",
                "#c45200","#0066cc","#5e5100","#0099cc","#3d4c00","#00bfff",
                "#734f00","#00cc99","#cc0000","#2e551f","#cc6600","#004d73",
                "#cc7a00","#005599","#cc8500","#0073b3","#cc9900","#0099ff",
                "#cca200","#0088cc","#b38600","#00aaff","#ccae00","#006699",
                "#ccba00","#0077b3","#cccc00","#0055cc","#cccf00","#004488",
                "#cdd200","#003366","#ccde00","#002d4d","#b9cc00","#004d6d",
                "#99cc00","#003366","#7fcc00","#00141d","#00ccc2","#002228",
                "#00cccf","#001014","#00ccdc","#000d0f","#00ccf3","#000a05")
```

预览：`assets/palettes/my55colors.png`

### color3list（25 组）

```r
color3list <- c("#1a8c42","#de452f","#3f5ba1","#c48e07","#2e6a9f",
                "#b34078","#5e9c14","#9449b6","#227272","#e87032",
                "#4b367b","#a6a535","#6c176e","#89b456","#d436a3",
                "#3b7f34","#b14624","#6c6995","#e3845e","#376080",
                "#cc8b19","#4d539e","#8d7520","#2b8d84","#c44b56")
```

预览：`assets/palettes/color3list.png`

### AM_Colors

```r
am_colors <- c("#0B1D2B", "#0D6EA6", "#67BFE6", "#A9D9EF",
               "#B7A1D6", "#E6A6C9", "#FFC857", "#F6F8FC")
```

预览：`assets/palettes/am_colors.png`

### nature color（5 套小色板，顶刊风）

```r
nature_color_1 <- c("#0ddbf5","#1d9bf7","#8386fc","#303cf9","#fe5357","#fd7c1a","#ffbd15","#fcff07")
nature_color_2 <- c("#444577","#c65861","#f3dee0","#ffa725","#ff6b62","#be588d","#58538b")
nature_color_3 <- c("#4292c9","#a0c9e5","#35a153","#afdd8b","#f26a11","#fe9376","#817cb9","#bcbddd")
nature_color_4 <- c("#cb78a6","#d35f00","#f7ec44","#009d73","#fcb93e","#0072b2","#979797")
nature_color_5 <- c("#b4403e","#256ea2","#ea841e","#399335","#603c87","#9d5c39")
```

预览：`assets/palettes/nature_colors.png`

### 其他小色板

```r
# tidyplots 提取（色盲友好，Okabe-Ito 系）
color_discrete_friendly <- c("#0072B2","#56B4E9","#009E73","#F5C710","#E69F00","#D55E00")
colors_discrete_seaside <- c("#8ecae6","#219ebc","#023047","#ffb703","#fb8500")
colors_discrete_friendly_long <- c("#CC79A7","#0072B2","#56B4E9","#009E73","#F5C710","#E69F00","#D55E00")
colors_discrete_friendly_long_2 <- c("#fe65b3","#CC79A7","#ffd2d8","#0072B2","#007aff","#56B4E9",
                                     "#009E73","#4cd964","#F5C710","#E69F00","#D55E00","#ff3b30")
colors_discrete_apple  <- c("#ff3b30","#ff9500","#ffcc00","#4cd964","#5ac8fa","#007aff","#5856d6")
colors_discrete_ibm    <- c("#5B8DFE","#725DEE","#DD227D","#FE5F00","#FFB109")
colors_discrete_candy  <- c("#9b5de5","#f15bb5","#fee440","#00bbf9","#00f5d4")

# 常规 4-6 色
color_1 <- c("#ECA669","#E06681","#8087E2","#E2D269")
color_2 <- c("#4DACD6","#4FAE62","#F6C54D","#E37D46","#C02D45","#8ecae6","#219ebc","#023047","#ffb703","#fb8500")
three_color <- c("#fce39a","#f66895","#c6e1b6")
```

预览：`assets/palettes/tidyplots_discrete.png`、`assets/palettes/friendly_long_2.png`、`assets/palettes/three_color.png`

## 3 连续 / 发散渐变色板

```r
# 蓝-粉-黄（适合表达量热图，顶端高亮突出）
colors_continuous_bluepinkyellow <- c("#00034D","#000F9F","#001CEF","#241EF5","#5823F6","#A033E0",
                                      "#E85AB1","#F1907C","#F4AF63","#FCE552","#FFFB6D")

# turbo（全色谱，适合大范围连续数值）
colors_continuous_turbo <- c("#30123BFF","#372365FF","#3D3489FF","#4147AEFF","#4456C8FF","#4669E0FF",
                             "#4777EFFF","#4688FBFF","#4197FFFF","#38A5FBFF","#2CB7F0FF","#22C4E3FF",
                             "#1AD3D1FF","#18DDC2FF","#1DE7B2FF","#29EFA2FF","#3DF58CFF","#53FA79FF",
                             "#69FD66FF","#83FF51FF","#98FE43FF","#AAFB39FF","#BAF635FF","#CBED34FF",
                             "#D9E436FF","#E4DA38FF","#F0CC3AFF","#F7C13AFF","#FCB136FF","#FEA230FF",
                             "#FE8F28FF","#FB7D21FF","#F56918FF","#EF5911FF","#E74A0CFF","#DD3C08FF",
                             "#D23105FF","#C32503FF","#B51C01FF","#A31301FF","#910B01FF","#7A0403FF")

# rocket（深紫-橙红-浅肤色，单峰型表达量）
colors_continuous_rocket <- c("#03051AFF","#0A091FFF","#120D25FF","#1C112BFF","#241432FF","#2E1739FF",
                              "#36193FFF","#411B44FF","#491D49FF","#531E4DFF","#5E1F52FF","#671F55FF",
                              "#721F57FF","#7B1F59FF","#871E5BFF","#921C5BFF","#9D1B5BFF","#A7195AFF",
                              "#B01759FF","#BC1656FF","#C51852FF","#CE1D4EFF","#D62449FF","#DE2E44FF",
                              "#E43841FF","#E8413EFF","#ED4F3EFF","#EF5A41FF","#F26747FF","#F3724EFF",
                              "#F47E57FF","#F58860FF","#F5946BFF","#F69D75FF","#F6A77FFF","#F6B28CFF",
                              "#F6BB97FF","#F7C6A6FF","#F7CEB2FF","#F8D8C1FF","#F9E0CEFF","#FAEBDDFF")
```

预览：`assets/palettes/continuous_bluepinkyellow.png`、`assets/palettes/continuous_rocket.png`

Z-score 发散填充（热图常用）：

```r
scale_fill_gradient2(low = "#303cf9", mid = "#ffffff", high = "#fe5357")  # midpoint 默认 0，正好
```

## 4 ggsci 期刊色板

`ggsci` 用法：在 ggplot 对象后加 `scale_color_xxx()` / `scale_fill_xxx()` 即可；底层是 `discrete_scale` 的封装，所以 `discrete_scale` 支持的参数（如 `alpha`）都能用。连续型数据用 `pal_gsea()`（GSEA 色板是连续的）。

| 期刊 / 主题 | Scales | Palette 类型 | 提取函数 |
|---|---|---|---|
| NPG (Nature) | `scale_color_npg()` `scale_fill_npg()` | `"nrc"` | `pal_npg()` |
| AAAS (Science) | `scale_color_aaas()` `scale_fill_aaas()` | `"default"` | `pal_aaas()` |
| NEJM | `scale_color_nejm()` `scale_fill_nejm()` | `"default"` | `pal_nejm()` |
| Lancet | `scale_color_lancet()` `scale_fill_lancet()` | `"lanonc"` | `pal_lancet()` |
| JAMA | `scale_color_jama()` `scale_fill_jama()` | `"default"` | `pal_jama()` |
| BMJ | `scale_color_bmj()` `scale_fill_bmj()` | `"default"` | `pal_bmj()` |
| JCO | `scale_color_jco()` `scale_fill_jco()` | `"default"` | `pal_jco()` |
| Frontiers | `scale_color_frontiers()` `scale_fill_frontiers()` | `"default"` | `pal_frontiers()` |
| UCSCGB | `scale_color_ucscgb()` `scale_fill_ucscgb()` | `"default"` | `pal_ucscgb()` |
| D3.js | `scale_color_d3()` `scale_fill_d3()` | `"category10" "category20" "category20b" "category20c"` | `pal_d3()` |
| Observable | `scale_color_observable()` `scale_fill_observable()` | `"observable10"` | `pal_observable()` |
| LocusZoom | `scale_color_locuszoom()` `scale_fill_locuszoom()` | `"default"` | `pal_locuszoom()` |
| IGV | `scale_color_igv()` `scale_fill_igv()` | `"default" "alternating"` | `pal_igv()` |
| COSMIC | `scale_color_cosmic()` `scale_fill_cosmic()` | `"hallmarks_light" "hallmarks_dark" "signature_substitutions"` | `pal_cosmic()` |
| UChicago | `scale_color_uchicago()` `scale_fill_uchicago()` | `"default" "light" "dark"` | `pal_uchicago()` |
| GSEA (连续) | `scale_color_gsea()` `scale_fill_gsea()` | `"default"` | `pal_gsea()` |
| Bootstrap 5 | `scale_color_bs5()` `scale_fill_bs5()` | `"blue" "indigo" ... "gray"` | `pal_bs5()` |
| Material Design | `scale_color_material()` `scale_fill_material()` | `"red" "pink" ... "blue-grey"` | `pal_material()` |
| Tailwind CSS | `scale_color_tw3()` `scale_fill_tw3()` | `"slate" "gray" ... "rose"` | `pal_tw3()` |
| 影视主题 | Star Trek / Tron Legacy / Futurama / Rick and Morty / The Simpsons / Flat UI | 见各 `scale_xxx()` | `pal_xxx()` |

提取色值供其他用途（如 ComplexHeatmap、自定义 scale）：

```r
library(ggsci)
pal_npg("nrc")(10)
# [1] "#E64B35FF" "#4DBBD5FF" "#00A087FF" "#3C5488FF" "#F39B7FFF"
# [6] "#8491B4FF" "#91D1C2FF" "#DC0000FF" "#7E6148FF" "#B09C85FF"
```

## 5 系统色板

```r
# RColorBrewer：三类——seq 连续 / div 发散 / qual 定性
library(RColorBrewer)
display.brewer.all(type = "seq")   # 单渐变：一种颜色由浅到深
display.brewer.all(type = "div")   # 双渐变：两端深、中间浅
display.brewer.all(type = "qual")  # 区分色：高区分度分类色

# grDevices::hcl.colors()：现代化的感知均匀色板（推荐 palette = "Rocket" "Turbo" "Berlin" "Fall" "Sunset"）
hcl.colors(n, palette = "Rocket")
```

## 6 动态生成颜色

```r
# 任意两端色生成渐变（space="Lab" 感知均匀）
library(dichromat)
discrete_palette <- colorRampPalette(c("#303cf9", "#fe5357"), space = "Lab", bias = 1, interpolate = "linear")
my_colors <- discrete_palette(60)

# 随机生成高区分度颜色
library(randomcoloR)
randomColor(count = 60)          # 随机（可能含近似色）
distinctColorPalette(60)         # 差异明显的 60 种

# scCustomize 调色（单细胞常用）
scCustomize::scCustomize_Palette(num_groups = 36, ggplot_default_colors = FALSE)
```

`colorRampPalette` 的 `bias` 参数（调整渐变"重心"）：

- `bias = 1`（默认）：颜色均匀过渡。
- `bias > 1`：颜色向高端稀疏，低值区占据更多色阶空间。
- `bias < 1`：颜色向低端稀疏，高值区占据更多色阶空间。
- 场景：基因表达大部分集中在低值区、只有极少数极高值时，默认设置下高值根本看不出来；调大 `bias` 让高亮颜色集中在极少数 outliers 上。

## 7 颜色可视化工具

对色板向量的 hex 值做可视化预览（方便比较和挑选）：

```r
color_visibility <- function(color_list,
                             name = "Palette",
                             width = 1, alpha = 1,
                             text_size = 3) {
  require(ggplot2)
  plot_data <- data.frame(color = color_list, x = factor(seq_along(color_list)))
  p <- ggplot(plot_data, aes(x = x, y = 1, fill = color)) +
    geom_bar(stat = "identity", width = width, alpha = alpha) +
    scale_fill_identity() +
    geom_text(aes(label = color), y = 0.5, angle = 90, size = text_size) +
    labs(title = name) +
    theme_void() +
    theme(plot.title = element_text(size = 12,
                                    face = "bold", hjust = 0.5, margin = margin(b = 10)))
  return(p)
}

color_visibility(nature_color_5, "nature_color_5")
```
