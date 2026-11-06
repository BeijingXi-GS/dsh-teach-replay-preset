# B站教学视频复现教练（DSH Agent 预设 · teach-replay）

> 一句话定位：给一条（或几条）B 站教学视频，读出「这位老师为什么这样教」，
> 结合受众画像与评论区争议，产出**能直接上课**的成品：
> 视频解剖报告、教学设计、教学课件、教学视频提示词与分镜、评论讨论、复现自检表。

> English abstract: A DeepSeek Harness agent preset that turns Bilibili teaching videos into
> reproducible lesson plans. For one video it reverse-engineers the teacher's structure,
> knowledge chain, teacher moves, student activities, assessment alignment and audience fit;
> for several videos it builds a comparison matrix, explains the differences and recommends a
> choice or a merged design. Outputs include a dissection report, a lesson plan (.docx),
> slides (.pptx), prompt/shot lists for producing your own video, and a self-check list.
> Two hard requirements: every concrete example taken from the original video must be
> re-instantiated (same teaching function, different local shell, never copied verbatim) with a
> replacement table, and slides must implement step-by-step revelation as 2–4 consecutive pages
> (multi-slide animation) instead of relying on built-in animation effects.
> Requires the `dsh-bili-watch` plugin (for video content) and the built-in office skills.

## 解决什么问题

| 你的痛点 | 它给什么 |
|---|---|
| 「名师课很好，但说不出好在哪」 | 视频解剖：结构分段 / 知识发生链 / 教师行为 / 学生活动 / 评价一致性，每条附 `[mm:ss]` 证据 |
| 「照搬名师课，自己班上不动」 | 受众四维画像 + 原班/普通班/异质班三档改造，具体到替换哪个动作 |
| 「备课来不及，要现成教案和课件」 | 教学设计 .docx + 教学课件 .pptx，与解剖报告逐环节对齐 |
| 「几条同类视频，选哪条」 | 对比矩阵 + 差异归因（受众/目标/条件）+ 选型与融合决策树 |
| 「想自己录/生成微课」 | 口播稿 + 分镜 + AI 生成画面的提示词 |
| 「原片的例题/情境，自己班学生不买账」 | 示例同构替换：原片的情境/道具/数字/作品换成本地可得、本质相同的示例，附「原示例→替代示例」对照表 |
| 「PPT 想一步步出现，又做不出动画」 | 课件多页动画：关键认知过程拆成 2–4 页连续画面，每页只多一个信息点，备注写「什么信号才翻页」 |
| 「评论区到底在吵什么」 | 事实/情绪/建议三分类，挑真争议给判断，不摘抄情绪 |

## 它做什么

| 输入 | 输出 |
|---|---|
| **1 条教学视频** | 视频解剖报告、教学设计（.docx，含示例同构替换表）、教学课件（.pptx，含多页动画序列）、教学视频提示词与分镜（.md）、评论与争议讨论（.md）、复现清单+替换表+动画序列表（.md） |
| **多条教学视频** | 对比矩阵、共性与差异分析（按受众/目标/条件归因）、评论证据与讨论、融合方案与决策树 |

单视频的重点是**复现这位老师为什么这样教**：结构、知识发生链、教师行为、学生活动、
评价一致性、受众适配；多视频的重点是**总结骨架 + 解释差异 + 按学情选型/融合**。

## 两种用法

### 一、单视频：把一位老师的课读透，产出一套能照着上的课

输入可以是 BV 号、分享链接，或直接粘贴字幕文本（没有 `bili_watch` 时的降级路径）。

产出与顺序：

1. **视频解剖报告**——结构分段（每段 `[mm:ss]`）、知识发生链、教师行为、学生活动、
   评价一致性、受众适配，每条结论标注证据来源。
2. **教学设计（.docx）**——按你给的学段/课时/班型改写，含目标、环节、时间、教师话术要点。
3. **教学课件（.pptx）**——与教学设计逐环节对齐；关键认知过程用 2–4 页连续画面做「多页动画」，
   每页只增一个信息点，备注写「接上页什么 / 本页新增什么 / 什么信号才翻页」。
4. **教学视频提示词与分镜（.md）**——要自己录/自己生成时用。
5. **评论与争议讨论（.md）**——评论区里的事实/情绪/建议三分类，对照视频做法挑真争议。
6. **复现清单、示例同构替换表与课件动画序列表（.md）**——逐条可勾选，防止「看懂了但没做」；
   替换表按「原示例 | 原片时间 | 教学功能 | 替代示例 | 同构理由 | 差异与风险」写，动画序列表按页序写。

### 二、多视频：总结异同，选出最适合你学情的一条（或融合）

输入 2–5 条同主题视频，产出：

- **对比矩阵**：维度 × 视频，逐格给证据与 `[mm:ss]`；
- **共性与差异归因**：差异按受众、目标、条件（课时/设备/班额）解释，而不是只说「讲得不一样」；
- **选型建议**：给「原班 / 普通班 / 异质班或线上」三档改造方案，具体到替换动作；
- **融合方案与决策树**：哪条取哪一段、为什么，砍掉的部分怎么补。

## 关键设计

1. **证据可溯源**：每条结论标注【字幕/画面/评论/推断】+ `[mm:ss]`；字幕错别字可纠正但不得改变意思；
   绝不编造。
2. **受众决定方案**：认知起点/发展阶段/动机注意力/教学条件四维画像，必须给「原班 / 普通班 /
   异质班或线上」三档改造，具体到替换动作。
3. **只教可复现动作**：禁止「激发兴趣/培养素养/因材施教」等不可观察表述，
   一律翻译成教师行为与判断信号。
4. **对公开课批判**：表演性成分单列（精选学生、提前磨课、设备资源），给常态课降级方案。
5. **评论做讨论不做摘抄**：事实/情绪/建议三分类，评论与视频做法对照，挑真争议并给判断。
6. **示例同构替换**：学的是教学功能，不是原片外壳。导入情境、故事、道具、数字、人物、作品、练习题
   一律换成本质相同、外壳不同、本地可得的示例，并给替换表；数学对象/概念/教材指定例题属「内容本体」
   不换（只换教法）。直接照抄原片示例视为不合格交付。
7. **课件用多页动画**：拆页序列代替内建动画（拆页、同位置替换、叠层遮罩、对比翻转、聚光灯放大、
   路径页），每页只增一个信息点、备注写翻页卡点，至少 3 组序列；纸质讲义/PDF 导出同样可用。

## 安装

**前置条件**：

- DeepSeek Harness 桌面版（`desktop` profile）、Node ≥ 22；
- **`dsh-bili-watch`**：提供 `bili_watch` 工具（读字幕 / 评论 / 弹幕 / 抽帧 OCR），
  按该插件自己的说明装进 profile；
- **内置 office 技能**：`office` / `office-docx` / `office-pptx`（生成并校验 .docx / .pptx）。

```text
# 1) 克隆仓库（仓库地址以你 fork/克隆的为准）
git clone <你的仓库地址> dsh-teach-replay-preset
cd dsh-teach-replay-preset

# 2) 把技能目录的绝对路径写进 cordis.patch.yml
#    DSH 的 skill-filesystem 用 path.resolve() 解析 bundledSkillDir，相对路径不可靠，
#    所以仓库里这一行默认是注释掉的，必须跑一次脚本写入本机绝对路径。
node scripts/set-skill-dir.mjs

# 3) 安装 bundle
plugin_manager action=install_bundle target="<克隆后的仓库绝对路径>"
```

安装后**新建会话**，在 Agent 预设列表选择「B站教学视频复现教练」。已有会话不会切换到新预设。

> 跳过第 2 步也能用：persona 里已写「技能加载失败就按提示词框架继续」的兜底，
> 只是拿不到 SKILL.md 里的解剖框架、统计口径、模板与 rubric。

卸载：`plugin_manager action=remove_bundle target="dsh-teach-replay-preset"`，
或从 profile 的 `package.json` 的 `dsh.profile.bundles` 里移除后执行 `pnpm install`。

## 使用示例

- 单视频（BV 号或链接）：

  ```text
  BV1KdZrBuEwP 这条密铺公开课，帮我读透，给可以照着上的教案和 PPT，我要教四年级。
  ```

- 多视频对比 + 选型：

  ```text
  这三条视频（链接1/2/3）都是讲概念的，帮我总结异同，我们学校没平板、班额 52，选哪条改最合适？
  ```

- 课时压缩：

  ```text
  这条名师课我只有 40 分钟课时，帮我判断哪些环节能砍、砍了怎么补。
  ```

- 二次创作：

  ```text
  照这条视频的讲解思路，写一个 10 分钟微课的口播稿和分镜，再给我 AI 生成画面的提示词。
  ```

- 换成本地示例（不照搬原片）：

  ```text
  原片用的情境我们学校没有，帮我按教学功能换成本地可得的示例，给替换对照表，别改知识点。
  ```

- 课件分步动画：

  ```text
  课件按「概念形成 / 原理推导 / 反例辨析」各做一组多页动画，每页只多一个信息点，备注写翻页卡点。
  ```

- 只有字幕文本（无 `bili_watch` / 视频无字幕）：

  ```text
  字幕我贴在这里：<字幕>。按这条视频的做法给我教案和 PPT，缺画面信息的地方标注可信度。
  ```

## 目录结构

```text
dsh-teach-replay-preset/
├── package.json                  # bundle 元数据（dsh.bundle.patch 指向 cordis.patch.yml）
├── cordis.patch.yml              # preset 声明：persona（身份/铁律/流程/产物规范/质量门）+ 工具与技能
├── scripts/
│   └── set-skill-dir.mjs         # 把本仓库 skills/ 的绝对路径写入 bundledSkillDir
├── skills/
│   └── teach-replay/
│       ├── SKILL.md              # 深度工作单：解剖框架、统计口径、示例同构替换、多页动画、模板、对比矩阵、rubric
│       └── references/
│           └── ppt-multislide-animation.md   # 多页动画手法详解（七种手法/页序写法/翻车对照）
└── README.md
```

`persona` 是常驻系统提示（约 6k 字，自包含可独立工作）；`SKILL.md` 按需加载
（模型判断需要细节时调用 `skill` 工具）。两者分工：常驻部分管「不能违反什么」，
技能部分管「具体怎么做」。

## 依赖与兼容性

- **DSH**：桌面版（`desktop` profile），随包工具 `plugin_manager` / `skill`。
- **Node**：≥ 22（已在 Node 22.19 上验证；`scripts/set-skill-dir.mjs` 无第三方依赖）。
- **平台**：已在 Windows 11 上验证；persona 与技能都是提示词，跨平台行为一致。
- **外部依赖**：`dsh-bili-watch`（读视频内容）、office 技能族（产出 docx/pptx）。
  这两项都由 npm / 内置提供，**不随本仓库分发**。

## 已知限制（含降级路径）

1. **没有 `bili_watch`**：Agent 会说明缺什么，可用「粘贴字幕文本」继续，其余流程不变；
   画面层结论（板书、演示、镜头）会标注为不可得。
2. **没有 office 技能**：教学设计/课件改成 Markdown 交付，并标注未生成 docx/pptx。
3. **视频无字幕且本地 ASR 不可用**：只能做元数据/评论/画面层分析，报告会明确标注可信度降级。
4. **评论区只是样本不是全体**：评论反映的是「愿意留言的人」，结论要写明样本局限，
   不能把几条热评当成全班学情。
5. **公开课的表演性无法完全剥离**：会单列并给常态课降级方案，但真实效果仍需你自己试讲验证。
6. **多页动画靠页数换节奏**：课件从 12–20 页扩到 16–24 页，讲义页数随之增加；PPTX 不依赖内建动画，
   动画感由页序实现，导出 PDF / 打印讲义时不会丢信息，但需要按页翻、按备注念。
7. **示例同构是判断，不是机械填空**：替换表能逼出「这个例子到底在教什么」，但替代示例是否真的同构、
   会不会引入新障碍，仍需教师按自己班学情复核。
8. **`bundledSkillDir` 必须跑 `node scripts/set-skill-dir.mjs`**，否则技能不出现（预设本身仍可用）。

## 来源、数据与版权说明

- 视频内容一律通过 `bili_watch` 在**会话内按需读取**，仓库里**不存放**任何视频、
  字幕原文或评论原文；仓库中的技能文档只含方法论、统计口径与模板。
- 涉及 B 站来源时，结论只引用 **BV 号 + 时间戳 + 结构化结论**（谁在什么位置说了什么类型的做法），
  不搬运整段文案；评论区只做事实/情绪/建议三分类的统计与转述。
- 产出物（教案、课件、口播稿）是基于方法论的**原创改写**，不是原视频的逐字稿；
  引用他人观点时请在成品里注明出处。
- `dsh-bili-watch` 与 office 技能为第三方/内置依赖，遵循其各自许可。

## 开源协议

[MIT](LICENSE) © 2026 Huhehaotedong