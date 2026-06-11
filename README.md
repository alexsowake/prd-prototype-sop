# PRD → Prototype SOP

一个 Claude Code Skill，帮你把产品想法变成能直接粘进 [Stitch](https://stitch.withgoogle.com) 的原型 prompt。

**适合谁用**：产品经理、独立开发者、创业者——不需要会写代码，也不需要懂设计工具。

---

## 这个 Skill 做什么

```
你给框架  →  它写 PRD  →  确认布局  →  给你 Stitch prompt  →  你粘进去出原型
```

全程有两个确认关卡，保证布局在零成本阶段就改对，不浪费 Stitch 生成时间。

**分工说明**：本 Skill 负责写 PRD、出 ASCII 草图、产出 prompt。生成原型这步由你自己把 prompt 粘进 [Stitch 网页版](https://stitch.withgoogle.com) 完成——手动粘贴 2 分钟出图，比 MCP 自动化更快也更可控。

---

## 安装

把整个目录放进你的 Claude Code skills 文件夹：

```bash
git clone https://github.com/alexsowake/prd-prototype-sop.git ~/.claude/skills/prd-prototype-sop
```

重启 Claude Code 后，输入 `/prd-prototype-sop` 即可启动。

**前置要求**：[Claude Code](https://claude.ai/code) CLI 已安装并登录。其他依赖（`enhance-prompt` 等）Skill 启动时会自动检测并安装，无需手动配置。

---

## 使用流程

启动后 Skill 会引导你完成一次性配置（PRD 存哪、资产从哪读、定稿发哪），之后直接进入主流程：

1. **你给产品框架**：一句话描述也行，越清楚越好
2. **Skill 写 PRD**：读懂你的需求后细化，等你确认（关卡 A）
3. **ASCII 草图**（复杂页面才有）：确认布局比例（关卡 B）
4. **产出 Stitch prompt**：code block 格式，直接复制
5. **你粘进 Stitch**：选好 Platform 模式（Mobile / Web），生成原型

---

## 注意事项

- **Stitch 横屏限制**：Stitch 出不了真横屏比例。如果你的产品有横屏页面（如视频播放器），Skill 会主动提示解决方案。
- **Stitch 免费使用**：[stitch.withgoogle.com](https://stitch.withgoogle.com)，Google 账号登录即可，无需付费。

---

## 文件结构

```
prd-prototype-sop/
├── SKILL.md                      # Skill 主逻辑
└── references/
    └── stitch-prompt-guide.md    # Stitch prompt 写作规范（三条质量铁律、多屏排列、设计系统等）
```
