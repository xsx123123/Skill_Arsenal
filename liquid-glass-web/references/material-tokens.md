# 材质 Token 与 CSS 参考

## 目录

- [色板](#色板)
- [页面环境背景](#页面环境背景)
- [玻璃表面基础 CSS](#玻璃表面基础-css)
- [五层材质分级](#五层材质分级)
- [推荐 CSS 变量组织](#推荐-css-变量组织)
- [降级与辅助设置](#降级与辅助设置)

## 色板

前三个是环境底色，后四个是界面前景色。铺到页面时结合下方 CSS 中的 alpha 与渐变范围使用。

| 用途 | 色值 | 说明 |
| -- | -- | -- |
| 暖杏环境光 | `#FFD29D`，α .46 | 左侧边缘，提供暖色参照 |
| 冷蓝环境光 | `#A0CFFF`，α .50 | 右侧边缘，与暖色相平衡 |
| 页面底色 | `#F7F9FC` | 连接两侧环境色的浅底 |
| 主操作蓝 | `#2868D8` | 集中强调最重要的动作 |
| 正文墨色 | `#202C40` | 文字始终不透明 |
| 次要文字 | `#5B6D85` | 辅助信息 |
| 玻璃白色填充 | `#FFFFFF + alpha` | 覆盖量越高，背景越不明显 |

## 页面环境背景

色彩留在边缘、中央白净；不要用高频彩虹背景，会抢走内容。

```css
body {
  background:
    radial-gradient(70rem 45rem at -8% 6%,
      rgba(255, 210, 157, .46), transparent 55%),
    radial-gradient(70rem 44rem at 108% 9%,
      rgba(160, 207, 255, .50), transparent 58%),
    #f7f9fc;
}
```

## 玻璃表面基础 CSS

半透明填充决定白色覆盖，高光描边定义轮廓，模糊处理背后内容，阴影负责层级分离。

```css
.glass-surface {
  background: linear-gradient(145deg,
    rgba(255,255,255,.78), rgba(255,255,255,.42));
  border: 1px solid rgba(255,255,255,.82);
  box-shadow:
    inset 0 1px 0 rgba(255,255,255,.84),
    0 18px 48px rgba(55,83,118,.14);
  -webkit-backdrop-filter: blur(24px) saturate(130%);
  backdrop-filter: blur(24px) saturate(130%);
}
```

**不要使用父容器 `opacity`**：它会让文字、图标、输入框和焦点一起变淡。让背景颜色带 alpha，子内容保持不透明。

## 五层材质分级

把不同层级放在同一背景上比较：观察卡片区的线条、导航区的色彩渗透、阅读区的白色覆盖。数值是教学起点，可按内容与背景调整。

| 层级 | 白色覆盖 | blur | 用途 |
| -- | -- | -- | -- |
| 导航 | 42% | 24px | 环境色最明显 |
| 工具条 | 58% | 18px | 提高控件辨识度 |
| 卡片 | 72% | 0px | 减少额外模糊 |
| 模态框 | 86% | 30px | 强化当前焦点 |
| 阅读面 | 96% | 0px | 接近实色白底 |

Apple 官方指导把 Liquid Glass 主要用于导航与控件层，并警告不要「玻璃叠玻璃」。继承这个层级原则，不要机械复制某个 blur 数字。

## 推荐 CSS 变量组织

把可调参数提出来，迭代时一次只动一组：

```css
:root {
  /* 环境色 */
  --env-warm: rgba(255, 210, 157, .46);
  --env-cool: rgba(160, 207, 255, .50);
  --page-bg: #f7f9fc;

  /* 材质分级 */
  --glass-nav-alpha: .42;     --glass-nav-blur: 24px;
  --glass-toolbar-alpha: .58; --glass-toolbar-blur: 18px;
  --glass-card-alpha: .72;    --glass-card-blur: 0px;
  --glass-modal-alpha: .86;   --glass-modal-blur: 30px;
  --glass-reading-alpha: .96; --glass-reading-blur: 0px;

  /* 结构 */
  --radius-surface: 20px;   /* 18~22px */
  --radius-control: 11px;   /* 10~12px */
  --space-1: 8px; --space-2: 12px; --space-3: 16px; --space-4: 24px;

  /* 语义色 */
  --accent: #2868D8;
  --text-primary: #202C40;
  --text-secondary: #5B6D85;
}
```

## 降级与辅助设置

显式 fallback 是实现的一部分，不是锦上添花。

```css
/* 不支持 backdrop-filter 时用实色底 */
@supports not ((-webkit-backdrop-filter: blur(1px)) or
               (backdrop-filter: blur(1px))) {
  .glass-surface { background: #f8fbff; }
}

/* 用户要求减少透明度 */
@media (prefers-reduced-transparency: reduce) {
  .glass-surface {
    background: #fff;
    -webkit-backdrop-filter: none;
    backdrop-filter: none;
  }
}

/* 高对比度 */
@media (prefers-contrast: more) {
  .glass-surface { border-color: #596f8e; }
}

/* 减少动态 */
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: .01ms !important;
    transition-duration: .01ms !important;
  }
}
```
