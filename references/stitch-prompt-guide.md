# Stitch Prompt 写作规范

> 产出第三步的 prompt 前读这份。目标：一段**结构化、具体、零占位**的 prompt，用户粘进 Stitch 就能出顺眼草稿。
> 沉淀自官方 `enhance-prompt` 增强管线 + 多轮实测。

## 必走的 enhance-prompt 管线

1. 先按"标准结构"写**初版 prompt**——**先不要发给用户**。
2. **调用官方 `/enhance-prompt` skill** 优化这版（用户硬性要求，不许跳过）。它是 Read/Write 转换、在当前上下文里跑，会强化 UI/UX 术语、补全编号小节、加调性形容词。
3. enhance-prompt **每次都会自动塞一段 `DESIGN SYSTEM (REQUIRED)`、里面是它现编的 hex 颜色**。默认**把这段整块删掉**再交付——原因：Stitch 收不到设计系统时会自动生成一套、单 project 内部一致性没问题（官方示例 + 实测确认），而 enhance-prompt 编的颜色没有任何真实依据、对不上任何资产。只在下面"何时保留设计系统块"成立时才保留 / 替换它。
4. 删干净后，按"交付格式"用 code block 给用户。

> 即：调用 enhance-prompt 是为了拿它的结构与术语增强，不是它编的颜色。别把现编的设计系统当真。

## 标准结构（默认，不含颜色字体）

```
[Overall Purpose: 一句话——这是什么页 + 给谁 + 什么调性]

**PLATFORM:** Mobile / Web，[Mobile/Desktop]-first

**PAGE STRUCTURE:**
1. **[区块名]:** 具体元素 + 真实文案
2. **[区块名]:** ...
```

- 调性用形容词（"clean, minimal, generous whitespace"），但**不写**主色 hex、字体名、圆角——交给 Stitch 自动设计系统。
- 区块用**编号小节**，每节一行核心功能 + 具体元素，帮 Stitch 理解层级。

## 三条质量铁律

1. **组件命名具体**，模糊词换成 UI 术语：
   - ❌ "顶部菜单" → ✅ "navigation bar with logo (left) and menu items (right)"
   - ❌ "一个按钮" → ✅ "primary call-to-action button '开始训练'"
   - ❌ "一些卡片" → ✅ "3-column card grid: thumbnail, title, 2-line description, category tag"
   - 术语词库见 `enhance-prompt/references/KEYWORDS.md`，不必背、按需查。
2. **内容必须真实**，绝不占位：真实文案、真实价格、真实标题日期。禁 Lorem Ipsum、禁 "Item 1/2/3"、禁 "John Doe / Acme Corp"、禁假整数（"99.99%"）、禁编造统计。
3. **不点名要 Stitch 生成具体图片**（"用电影剧照做背景""生成真实海报""生成 logo"）——它会把每张图单独生成成一块画布 tile，夹在真 UI 屏之间显乱。让它用默认占位图填充即可。

## 多屏交付：按功能分组 + 横向排列

Stitch 会把**同一段 prompt** 生成的多个 screen 排在画布**同一行**。利用这个特性组织交付：

- **同一个功能 / 同一组原型的多屏，放进同一个 code block**；下一个功能另起一个 code block。
- 每个含多屏的 code block **开头第一句**就写 `...arranged horizontally`，不要埋在中间。
- **对每个功能组的 prompt 分别跑一次 enhance-prompt**（而不是把整份 spec 一次性增强后再切），各组分别删掉设计系统块、分别交付。

## 何时保留"设计系统块"（默认删，例外才留）

只有以下两种情况，才保留 / 注入一段**真实 token** 的设计系统块：

- **需要跟既有设计资产 / 已定品牌保持一致**；
- **跨 project 一致性**——见下面"Mobile 单模式与横屏限制"，横屏屏单开 project 时，要把主 project 的设计系统注进去。

```
**DESIGN SYSTEM (REQUIRED):**
- Platform: Mobile / Web, [Mobile/Desktop]-first
- Theme: Light / Dark, [调性形容词]
- Background: 描述名 (#hex)
- Primary Accent: 描述名 (#hex) for [角色，如 CTA/链接]
- Text Primary: 描述名 (#hex)
- Buttons / Cards: 圆角 + 阴影描述
```

- token 从 `designs-work/` 对应产品的设计资产、或从主 project 提取的 DESIGN.md 里**提取**，不自己编（enhance-prompt 现编的那套一律不算数）。
- 颜色写成 `描述名 (#hex) for 角色`（如 `Deep Ocean Blue (#1a365d) for primary buttons`）。
- 多屏 / 多 project 要一致：同一段设计系统块**逐字复用**到每屏 / 每个 project 的 prompt（官方 baton-loop 确认这是首要一致性机制）。
- **兜底**：用户也可以粘 prompt 的同时在 Stitch 里**直接上传设计资产**锁一致性——这时 prompt 仍只写布局+内容，不带设计系统块。

## Mobile 单模式与横屏限制（实测，重要）

实测确认的两条硬限制：

1. **一个 Stitch project 只能 Mobile / Web 二选一，没法混。**
2. **Stitch 出不了真横屏。** 即便在 Web 模式、prompt 里写明 16:9，它也保不住横向比例（查官方文档 + 社区确认不支持）。接受这个限制，别指望 prompt 能救。

所以：**竖屏为主、夹少量横屏屏的产品（如视频 App 的播放器全屏），按这个流程做：**

1. **主 project 用 Mobile 模式，先做完所有竖屏 screen。**
2. **横屏屏放到后面、单独新开一个 project（Web 模式）再做。**
3. **把主 project 的设计风格提取出来，注入横屏 project 的 prompt**，保证两个 project 视觉统一——这正是上面"何时保留设计系统块"的跨 project 一致性场景：
   - 用 `design-md` skill 从主 project 提取 DESIGN.md（或从 `designs-work/` 资产提取）；
   - 把这段设计系统块逐字写进横屏 project 的 prompt。
4. 横屏屏的 prompt 描述成"手机横持的全屏播放器 / full-bleed 16:9 全幅画面 + 叠加半透明控制条"，并提醒用户在该 project 选 Web/Desktop 模式。**比例做不到真 16:9，接受近似。**

> 次选（能省事时）：接受主 project 里用竖屏帧近似——多数视频 App 本就竖屏浏览 + 运行时旋转，竖屏帧加一个"点击进入全屏"状态通常够用，可免去单开 project。

## 发 prompt 前自检（自己过，不打断用户）

prompt 不设确认关卡，但给出前逐条过一遍，任一不过就重写：

- [ ] 有 Overall Purpose + PLATFORM + 编号 PAGE STRUCTURE 三段
- [ ] 每个元素都具体、命名了组件，无 vague 占位
- [ ] 文案全真实，无 Lorem / 无假数字 / 无 "John Doe"
- [ ] 没点名要生成具体图片
- [ ] 已跑过 enhance-prompt；默认已删掉它现编的设计系统块（除非要对齐既有资产 / 跨 project 一致）
- [ ] 多屏分组：同组同 code block、开头写 `...arranged horizontally`
- [ ] 横屏屏：已按"单 project 单模式 + 单开 project + 注入设计系统"处理，并提醒用户切 Web 模式
- [ ] 用 Markdown code block 给出，用户可直接复制
