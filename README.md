# Skill Arsenal

个人自用的 AI Agent Skills 仓库，收录我在日常工作中沉淀的 Agent 技能（Skills）。每个 Skill 是一个独立目录，遵循标准的 `SKILL.md` 规范，可以被 Claude Code / Kimi Code 等支持 Skills 的 Agent 加载使用。

## 什么是 Skill

Skill 是一种可复用的能力包：一份带 `name` 和 `description` 的 `SKILL.md`（定义触发时机和工作流）+ 按需附带的 `references/`（详细参考文档）和 `assets/`（图片等素材）。Agent 在匹配到相关任务时自动加载对应 Skill，按其中的流程执行。

## 收录的 Skills

| Skill | 说明 |
|---|---|
| [ggplot-polisher](ggplot-polisher/SKILL.md) | 将 R ggplot2 图表打磨为 Cell / Nature / Science 等顶刊发表级水准的完整工作流：科研配色（个人精选色板 + ggsci 期刊色板）、patchwork / aplot / ggpubr 拼图排版、统计显著性标注、顶刊 Figure 排版规律与图注规范、出版级导出 |
| [liquid-glass-web](liquid-glass-web/SKILL.md) | 开发受 macOS 26 Liquid Glass（液态玻璃）启发的 Web 界面：设计 token 与五层材质分级、单文件 HTML 原型、响应式合同、无障碍降级与浏览器验收。仅为 Web CSS 模拟 |

## 目录结构

```
<skill-name>/
├── SKILL.md        # 入口文件：name / description / 工作流
├── references/     # 按需加载的详细参考文档
└── assets/         # 图片等素材
```

## 如何使用

- **本地使用**：将本仓库放在 Agent 的 Skills 扫描目录下（如 `~/.agents/skills/`），或通过软链引入：

  ```bash
  ln -s "$(pwd)/<skill-name>" ~/.agents/skills/<skill-name>
  ```

- **在项目中使用**：把对应 Skill 目录复制到项目的 `.agents/skills/` 或 `.kimi/skills/` 下。

Agent 会根据任务描述自动匹配并加载 Skill，也可以在对话中直接点名，例如「用 ggplot-polisher 美化这张图」。

## 新增 Skill

1. 新建 `<skill-name>/` 目录，在其中编写 `SKILL.md`，frontmatter 中写清 `name` 和 `description`（description 决定触发时机，要写清"什么时候用"和"什么时候不用"）。
2. 长文档、速查表放入 `references/`，图片素材放入 `assets/`。
3. 在上方表格中登记新 Skill。

## License

[MIT](LICENSE)
