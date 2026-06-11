# PRD → Prototype SOP（通用版）

把一个产品**框架**，经 PRD、布局确认，变成一段能直接粘进 [Stitch 网页版](https://stitch.withgoogle.com) 出原型的 prompt。

> 分工：本 skill 负责**写 PRD、出 ASCII 草图、产出 prompt**。**原型由用户自己把 prompt 粘进 [Stitch](https://stitch.withgoogle.com) 生成**——Stitch 出图慢、MCP 多轮不可控，手动粘贴反而 2 分钟出图，最敏捷。本流程不主调 Stitch MCP。

---

## 阶段 0：启动检测（每次 skill 启动时先跑）

### Step 1 — 读取已有配置

检查 memory 里是否存在 `prd-prototype-sop-config`（首次运行时由 Step 2 写入）：
- **已有配置**：读取后直接进入流程总览，不重复询问。
- **无配置**：进入 Step 2 引导。

### Step 2 — 首次配置引导（无配置时才执行）

依次询问用户以下三项，**全部确认后一次性存入 memory**，key 为 `prd-prototype-sop-config`：

**① PRD 草稿保存在哪里？**

| 选项 | 说明 |
|---|---|
| A. 本地目录 | 输入绝对路径（如 Obsidian vault 路径、任意本地文件夹）；草稿以 `.md` 文件写入该目录 |
| B. 仅在对话里展示 | 不写文件，PRD 内容直接输出在聊天窗口，用户自行复制保存 |

**② 设计资产从哪里读取？**

| 选项 | 说明 |
|---|---|
| A. 本地目录 | 输入绝对路径；skill 会直接读取该目录下的图片/文档 |
| B. 网络文档（飞书 / 钉钉 / Notion 等） | **先确认**：对应平台有没有可用的 CLI 或 MCP？无则推荐改选 A 或 C |
| C. 按需提供，无固定来源 | 每次用到时用户当场粘贴截图或文字，skill 不主动去取 |

**③ PRD 定稿发布到哪里？**

| 选项 | 说明 |
|---|---|
| A. 本地目录（与草稿同目录） | 定稿时去掉 `#Draft` 标签，文件留在本地 |
| B. 其他平台（Notion / Confluence 等） | 由用户自行从对话里复制 PRD 内容粘贴过去 |
| C. 不发布 | 仅在对话里展示定稿内容 |

### Step 3 — 依赖检测与安装

**必选：`/enhance-prompt` skill**

检查当前 session 的可用 skill 列表里是否有 `enhance-prompt`：
- **有**：继续。
- **没有**：**立即调用 `/find-skills enhance-prompt` 自动安装**，无需告知用户、无需等待确认。安装完成后继续；若 `/find-skills` 报错或找不到，再告知用户："enhance-prompt 安装失败，请联系提供本 skill 的作者获取安装方式，流程暂停。"

**可选：`/design-md`、`/taste-design`、`/stitch-loop`**

首次配置时，向用户介绍这三个可选 skill，询问是否安装：

| Skill | 作用 | 推荐场景 |
|---|---|---|
| `/design-md` | 从已有 Stitch project 提取设计规范（颜色、字体、圆角等），保证多个 project 视觉一致 | 做系列原型、横屏单开 project 时必用 |
| `/taste-design` | 检查 prompt 是否有"AI 味"（过度圆润、配色太俗、排版僵硬），主动优化 | 对视觉质量要求高时使用 |
| `/stitch-loop` | 多页原型一键批量生成，用"接力棒"模式保持各页风格一致 | 一次出 5 屏以上时效率翻倍 |

询问用户："以上三个 skill 是可选增强，现在要帮你安装吗？可以全选、单选或跳过。"
- 用户确认哪个安装，调用 `/find-skills <skill名>` 完成安装，安装结果告知用户。
- 用户跳过，继续。

（可选 skill 的安装结果也存入 `prd-prototype-sop-config` memory，下次启动不再重问。）

---

## 资产读取优先级

1. **用户当场提供**的文件 / 链接 / UI 截图（最高优先级）
2. 配置中的本地目录 / 网络文档
3. 对话历史中已有的上下文

不确定属于哪个产品/子目录，直接问，别猜。

---

## 流程总览

每个 🚦 关卡**必须停下等用户确认**，不准一口气跑到底——布局错要在零成本的 ASCII 阶段就改掉。

```
阶段0 检测与配置  见上方 Step 1-3。
① 框架           用户给。
② 写 PRD         读懂资产后细化 ──🚦关卡A：PRD 准确吗？
③ ASCII 草图      仅复杂 / 多模块页面画 ──🚦关卡B：布局合意吗？
④ Stitch prompt   过完 B 才写，按写作规范产出，code block 给用户，
                  同时附上 Stitch 入口：https://stitch.withgoogle.com
⑤ 用户粘贴生成    用户自己粘进 Stitch（注意选对 Platform 模式）。
```

---

## 红线

1. **未经用户明确同意，绝不对任何远程平台（飞书 / Notion 等）做写操作**——发布、新增、删除、修改一律先问。远程文档是对外的事实源，误改不可逆。读取可以，写入必须先拿到用户明确的"可以发"。
2. **草稿打 `#Draft` 标签，定稿去掉**。写 PRD 草稿时顶部加 `#Draft`；用户确认定稿后去掉该标签，再走发布。若草稿选 B（仅对话展示），则在输出顶部注明"草稿，待确认"。
3. **PRD 与 Stitch prompt 分离**。PRD 是给团队看的事实源，不嵌渲染图、不嵌 prompt；prompt 是一次性脚手架，单独用 code block 给。视觉意图在 PRD 里一律用**文字**描述。
4. **ASCII 草图按需**：复杂 / 多模块 / 讲比例的页面才画给用户确认；简单表单、单个弹窗直接出 prompt，别多此一举。
5. **横屏 Mobile 设计主动预警**：用户一旦提到横屏、横向布局、横持手机、landscape、全屏播放器、16:9 等关键词，**立即说明 Stitch 的限制**，再继续：
   > ⚠️ Stitch 限制提醒：一个 project 只能选 Mobile 或 Web 二选一，且出不了真横屏比例。建议：竖屏页面在主 project（Mobile 模式）完成，横屏页面单独新开一个 project（Web 模式），并把主 project 的设计风格注入横屏 project 的 prompt 保持一致（用 `/design-md` 提取后注入）。

---

## 第一步：写 PRD

- 用户给框架后先**读资产**（按上面的优先级），读懂再细化。资产里说不清的地方，问，别猜。
- 草稿按配置写入本地目录 / 在对话里展示，顶部加 `#Draft`。
- 🚦关卡A 交用户审；准确、用户说定稿，去掉 `#Draft`。
- 发布前**必须**拿到用户明确同意（红线1）。同意后按配置的发布方式操作。

## 第二步：ASCII 草图（按需）

只在复杂 / 多模块 / 有布局比例的画面，用纯文本框线画出页面骨架，做**最低成本**的布局确认。一屏一张，标清各区块位置与层级。用户在 🚦关卡B 确认布局后再进第三步。简单页面跳过。

## 第三步：产出 Stitch prompt

目标：一段**结构化、具体、零占位**的 prompt，用户粘进 Stitch 就能出顺眼草稿。

**核心动作（细节见下面的写作规范文件，产出前必读）：**
1. 先按标准结构写**初版 prompt**，先不要发。
2. **调用官方 `/enhance-prompt` skill** 优化（用户硬性要求，不许跳过）。
3. enhance-prompt 每次都会自动塞一段现编 hex 颜色的 `DESIGN SYSTEM (REQUIRED)` 块——默认**整块删掉**再交付（除非要对齐既有资产 / 跨 project 一致）。
4. 用 code block 给用户；多屏按功能组分块、每块开头写 `...arranged horizontally`。
5. **prompt 下方**附上一行入口提示：
   > 粘贴到 Stitch 生成原型：https://stitch.withgoogle.com （注意选对 Platform 模式：Mobile / Web）

➡️ **产出前先读 [`references/stitch-prompt-guide.md`](references/stitch-prompt-guide.md)**：标准结构、三条质量铁律、多屏横向排列、何时保留设计系统块、Mobile 单模式 / 横屏限制的处理、发 prompt 前自检清单——全在里面。

---

## 相关 skill / 工具

- `/enhance-prompt`：写作规范主要来源 + 术语库，**必须**——启动时自动检测并安装。
- `/design-md`：从 Stitch project 提取设计规范，保证多 project 视觉一致——**可选**，首次配置时询问安装。
- `/taste-design`：反"AI 味"审查 + 优化——**可选**，首次配置时询问安装。
- `/stitch-loop`：多页批量生成，接力棒风格一致性——**可选**，首次配置时询问安装。
