# LLM Knowledge Base Skill

## Claude Code 工具约定（必读）

> **此 skill 通过 CLAUDE.md 中的 `@llm-knowledge` 声明自动加载，不通过 `/skill` 命令调用。**
> 如果刚刚安装完 skill 并创建了 CLAUDE.md，需要**重新开启一个新会话**才能生效。

文件操作必须使用 Claude Code 内置工具，**禁止用 bash 写文件**（bash 沙盒屏蔽了 `cat`、`echo >` 等命令）：

| 操作 | 使用工具 | 禁止 |
|---|---|---|
| 创建新文件 | `Write` | `cat > file` / `echo > file` |
| 修改已有文件 | `Edit` | `sed` / `awk` |
| 读取文件 | `Read` | `cat` / `head` / `tail` |
| 搜索内容 | `Grep` | `grep` / `rg` |
| 查找文件 | `Glob` | `find` / `ls` |
| 创建目录 | `Bash` (仅 `mkdir -p`) | — |

---

## 用途
构建和维护个人知识库。知识库跟人走，不跟角色或项目走。
采用双层架构：
- **wiki/** — AI 查询层（Obsidian Flavored Markdown，token 友好）
- **atlas/** — 人类阅读层（Canvas / Excalidraw / Mermaid / Bases 数据视图）

---

## 核心设计原则

1. **知识库 = 个人资产**：一个人一个库，domain 随兴趣和成长演化
2. **双层产物**：同一份 raw/ 资料 → 编译为 wiki/（AI 读）+ atlas/（人读）
3. **LLM 是编译器**：raw/ 是源码，LLM 负责全部写入，人类只读
4. **直接文件操作**：Agent 直接写入 Obsidian vault 目录（Claude Code 用 Write 工具，其他 Agent 用对应文件操作能力）
5. **视觉驱动理解**：流程 → Mermaid；概念关系 → Excalidraw；领域全貌 → Canvas
6. **Schema 驱动**：CLAUDE.md 配置语言风格、domain 树、视觉触发规则
7. **Obsidian 原生语法**：概念页使用 Obsidian Flavored Markdown（callouts、highlights、wikilinks）

---

## Domain 三级层级体系

知识按三级分类管理：

```
L1: Domain（领域）    → academic, tech, lifestyle ...
L2: Category（分类）  → math, science, testing ...
L3: Topic（主题）     → 概念页文件名，不建目录
```

**Domain 树在 CLAUDE.md 中定义**，用 markdown 缩进列表：

```markdown
## Domain 配置

- tech/ — 技术
  - cs/ — 数据结构、算法、OS、网络
  - testing/ — 测试策略、框架、自动化
  - dev/ — 架构、设计模式、语言
- industry/ — 行业
  - ai/ — 人工智能行业格局、趋势
  - web3/ — DeFi、智能合约
- lifestyle/ — 生活
  - food/ — 美食、食谱
  - fitness/ — 运动、健康
```

规则：
- L1、L2 是目录，L3 是概念页文件名（不建子目录）
- 最多三级，允许只用两级（L2 下直接放概念页）
- 用户可随时通过对话新增/修改 domain，Claude 同步更新 CLAUDE.md 和目录结构
- 新增 L1 时 → 创建 `atlas/<L1>/_map.canvas`
- 新增 L2 时 → 创建 `atlas/<L1>/<L2>/_map.canvas`
- 新增 topic 时 → 更新所属 L2 的 `_map.canvas`

---

## Hub 文件命名规范

每个 L1/L2 目录必须有一个同名的 hub .md 文件（不用 `_index.md`）：

```
wiki/concepts/academic/          → academic.md   ← L1 hub
wiki/concepts/academic/maths/    → maths.md      ← L2 hub
wiki/concepts/academic/maths/01-patterns/ → patterns.md ← 章节 hub（大类时使用）
```

**禁止使用 `_index.md`** — Obsidian 关系图谱中所有同名文件显示为同一标签，无法区分。

---

## 关系图谱层级规范

要让关系图谱呈现 L1 > L2 > topic 的层级结构，必须建立**双向链接链**：

- 每个 L2 hub 文件底部写 `Part of [[L1-hub]]`
- 每个章节/概念页底部写 `Part of [[L2-hub]]`
- L1 hub 在正文中用 `[[L2-hub]]` 列出所有子分类

这样节点大小 = 被引用次数，自然形成 L1 最大、L2 次之、概念页最小的层级视觉。

---

## 工作簿配套 KB 模式

当知识库对应一本**实体教材/工作簿**（如学校课本、习题册）时，在对应 L2 目录下使用以下结构：

```
wiki/concepts/<L1>/<L2>/
├── <L2>.md              ← L2 hub（链接所有章节）
├── README.md            ← 工作簿说明 + 使用方法
├── syllabus/            ← 教材映射、课程衔接、先修知识
│   ├── textbook-mapping.md
│   ├── bridge-to-next-level.md
│   └── prerequisites.md
├── glossary/            ← 学科术语表（命令词、代数/几何/统计术语、符号）
└── 01-<chapter>/        ← 按教材章节编号的概念目录
    ├── <chapter>.md     ← 章节 hub（含 Part of [[L2-hub]]）
    └── <topic>.md       ← 概念页
```

**概念页 frontmatter 需增加字段：**
```yaml
workbook: P{page}          # 对应教材页码
corbett: "{video-number}"  # 或其他外部资源编号（Khan Academy ID 等）
```

外部资源绑定方式：在概念页 Definition 下方加一行：
```markdown
📹 **Corbett Maths:** Video {number} — corbettmaths.com
```

---

## 目录结构约定

```
<knowledge-base-root>/              ← 同时是 Obsidian vault 根目录
├── CLAUDE.md                       ← 个人配置（domain 树、语言风格、偏好）
├── raw/                            ← 原始资料（只读，LLM 不修改）
│   └── <type>-<topic>-<YYYYMMDD>.md
│
├── wiki/                           ← AI 查询层
│   ├── index.md                    ← AI 导航入口（domain 树 + 全部概念页链接）
│   ├── concepts/<L1>/<L2>/         ← 概念页按 domain 层级存放
│   ├── entities/                   ← 实体页（人物、组织、工具等）
│   └── log.md                      ← 操作日志
│
├── atlas/                          ← 人类阅读层（Obsidian 可视化浏览）
│   ├── overview.canvas             ← 全库鸟瞰图（text 节点 + wikilink 跳转 L1 canvas）
│   ├── dashboard.base              ← Obsidian Bases 数据视图（按 domain/tags/日期 动态过滤）
│   ├── <L1>/
│   │   ├── _map.canvas             ← 领域总图（text 节点 + wikilink 跳转 L2 canvas）
│   │   └── <L2>/
│   │       ├── _map.canvas         ← 分类地图（file 节点链接 wiki 页，可预览内容）
│   │       └── <concept>.excalidraw
│   └── ...
│
└── output/                         ← 查询结果 + 备考产物
    └── progress/                   ← 学习掌握度追踪（仅 study 模式）
```

---

## 可视化触发规则

| 内容特征 | 触发动作 | Skill |
|---|---|---|
| 包含流程 / 步骤 / 时序 | 嵌入 mermaid 代码块到 wiki 概念页 | `@mermaid-visualizer` |
| 包含多概念关系 / 层级结构 | 生成 atlas/<L1>/<L2>/<concept>.excalidraw | `@excalidraw-diagram` |
| 新增 L1/L2（或执行 Map 操作） | 生成或更新对应 `_map.canvas` | `@obsidian-canvas-creator` |
| 用户明确要求「画图 / 思维导图」 | 按类型选择对应 skill | 三选一 |

**Canvas 节点规范**：

| 场景 | 节点类型 | 说明 |
|---|---|---|
| 层级导航节点（overview / L1 / L2 map） | `type: "text"` | 描述内容 + 末尾一行 wikilink 跳转到下一级 canvas |
| 有内容的概念页（L2 map 内） | `type: "file"`, `file` 指向 wiki .md 路径 | 节点内预览内容，右上角箭头图标跳转到完整页面 |
| 待填充的概念 | `type: "text"` | 标题 + _(coming soon)_，有内容后改为 file 类型 |

**Canvas 层级导航规范**：

知识库 Canvas 分三个导航层级，每层可通过 wikilink 跳转到下一层：

```
overview.canvas          ← L0：全库鸟瞰
  └── <L1>/_map.canvas   ← L1：领域总图
        └── <L2>/_map.canvas  ← L2：概念地图（file 节点链接 wiki 页）
```

在 `type: "text"` 节点末尾加一行 wikilink 实现跨层跳转：

```
[[atlas/<L1>/_map.canvas|→ Open Map]]
[[atlas/<L1>/<L2>/_map.canvas|→ Open Map]]
```

导航节点内容示例：

```
## Maths

Algebra · Geometry · Statistics
Patterns · Proportion · Quadratics

[[atlas/academic/maths/_map.canvas|→ Open Map]]
```

**注意事项**：
- `type: "file"` 节点会自动扩展显示完整内容预览，这是预期行为
- 布局时预留足够间距，避免多个 file 节点内容重叠
- **禁止** `type: "file"` 指向 .canvas 文件（嵌套 canvas 显示为空白，无法阅读）
- `type: "text"` 中的 wikilink 必须指向**已存在的文件**，否则 Obsidian 会自动创建垃圾空文件
- 每个 canvas 都必须有一个 `type: "text"` 的标题节点（内容 `# 标题`，置顶，横跨全宽）

---

## 操作集

### Init — 初始化
**触发**：「初始化知识库」

1. 读取 CLAUDE.md 中的 domain 树
2. 创建 raw/、wiki/concepts/、wiki/entities/、atlas/、output/ 目录
3. 按 domain 树创建 wiki/concepts/ 和 atlas/ 的子目录结构
4. 为每个 L1 和 L2 目录创建 hub 文件：
   - **命名规则：文件名 = 目录名**（`academic/` → `academic.md`，`maths/` → `maths.md`）
   - **禁止使用 `_index.md`**：Obsidian 图谱中所有同名文件显示为同一标签，无法区分
   - L1 hub 内容：列出所有 L2 子分类，每项用 `[[L2-hub-name]]` 链接
   - L2 hub 内容：列出所有话题概念页，底部加 `Part of [[L1-hub-name]]`
5. 创建 wiki/index.md（含 domain 树导航）、wiki/log.md
6. 创建 atlas/overview.canvas（L1 节点）及各级 `_map.canvas`
7. 若 CLAUDE.md 不存在，提示选择场景模板

---

### Ingest — 摄入新资料
**触发**：「摄入 <URL 或资源>」

**数据源路由**：

| 输入 | 工具 |
|---|---|
| https:// 网页 | web_fetch |
| github.com/ 仓库 | GitHub MCP |
| youtube.com 视频 | bash + yt-dlp --write-auto-sub |
| arxiv.org 论文 | web_fetch |
| .pdf 文件 | bash + pdftotext |
| 图片 / 手写笔记 | Claude Vision |
| confluence:PAGE-xxx | Confluence MCP |
| jira:ISSUE-xxx | Jira MCP |
| gitlab:repo/path | GitLab MCP |

**执行步骤**：

**A. 获取**：调用对应工具 → Write raw/<type>-<topic>-<YYYYMMDD>.md

**B. 分析**：提取核心概念、实体、流程步骤、概念间关系，判断所属 domain（L1/L2）

**C. 写 wiki/**：
- 按 `{{concept_format}}` 新建或更新 wiki/concepts/<L1>/<L2>/<topic>.md
- 概念页底部加 `Part of [[L2-hub-name]]`（建立图谱向上链接）
- 使用 Obsidian Markdown 增强格式（参见下方「Obsidian Markdown 规范」）
- 若有流程/步骤 → 调用 `@mermaid-visualizer`，嵌入 mermaid 代码块
- 更新 wiki/entities/（如有实体）
- 更新 L2 hub 文件：在话题列表中加入新概念页的 `[[topic]]` 链接
- 更新 wiki/index.md 和 wiki/log.md
- 若 topic 属于尚未存在的 L1/L2 → 执行 Domain 操作创建目录和 hub 文件，再继续

**D. 写 atlas/**：
- 若有多概念关系 → 调用 `@excalidraw-diagram` → 生成 atlas/<L1>/<L2>/<concept>.excalidraw
- 调用 `@obsidian-canvas-creator` → 更新 atlas/<L1>/<L2>/_map.canvas（新增节点，连边，file 指向 wiki 路径）
- 更新上级 canvas（确保 L1 和 overview 节点存在）

**输出摘要**：列出 wiki/ 和 atlas/ 各自新建/更新的文件

---

### Query — 查询
**触发**：直接提问

- **< 100 篇**：Read wiki/index.md → 定位 → Read 相关页 → 回答
- **≥ 100 篇**：qmd query → Read 命中页 → 回答
- 回答 Write 到 output/ 存档

---

### Lint — 健康检查
**触发**：「lint」/ 「health check」

1. 扫描 wiki/ 全部文件：矛盾、孤立页、缺失反链、过时内容
2. 检查 `[[wikilinks]]`：所有链接目标文件是否存在，不存在则报告（防止 Obsidian 自动创建垃圾文件）
3. 检查 atlas/ Canvas 节点是否都有对应 wiki 页（清理无效链接）
4. 验证 CLAUDE.md domain 树与实际目录结构一致
5. 自动修复可确定问题，列出需人工确认的矛盾
6. 追加 log.md

---

### Map — 更新可视化地图
**触发**：「更新地图」/ 「map <领域>」

1. Read wiki/concepts/<L1>/<L2>/ 全部文件
2. 调用 `@obsidian-canvas-creator` 重新生成对应 `_map.canvas`
   - 每个概念一个节点，file 指向 wiki 路径
   - 按概念关系连边，按子分类分组
3. 更新上级 canvas
4. 追加 log.md

---

### Domain — 管理分类
**触发**：「添加分类 <名称>」/「调整分类」/ 对话中提到新兴趣领域

1. 更新 CLAUDE.md 中的 domain 树
2. 创建对应 wiki/concepts/ 和 atlas/ 子目录
3. 创建 hub 文件（**文件名必须等于目录名，禁止用 `_index.md`**）：
   - 新 L1：创建 `wiki/concepts/<L1>/<L1>.md`，内容含对 L2 的 `[[L2]]` 链接列表
   - 新 L2：创建 `wiki/concepts/<L1>/<L2>/<L2>.md`，底部加 `Part of [[L1]]`
   - 在父级 hub 文件中加入新分类的 `[[新L2]]` 链接（保持双向链接）
4. 创建新的 `_map.canvas`
5. 更新 atlas/overview.canvas 和 wiki/index.md
6. 追加 log.md

---

### Exam — 备考模式
**触发**：「备考 <科目> <日期>」

仅在 `{{mode}}` 包含 `study` 时激活：
1. Read wiki/concepts/<subject>/ 全部页面
2. 按重要程度 × 完整度排序 → Priority Revision List
3. 调用 `@obsidian-canvas-creator` 生成备考 Canvas（高优先级节点高亮）
4. 生成 Marp 复习卡片（正面问题 / 背面答案+记忆技巧）
5. 生成模拟题（4 种题型混合）：
   - 选择题（Multiple Choice）— 4 选 1，考核概念辨析
   - 填空题（Fill in the Blank）— 考核关键术语记忆
   - 匹配题（Matching）— 考核概念间关系
   - 简答题（Short Answer）— 考核理解和应用
6. 写入 output/exam-<subject>-<date>/
7. 更新掌握度记录 output/progress/<subject>-mastery.md

**掌握度追踪**（交互模式）：
- 测验后按概念评估掌握度：🟥 weak / 🟨 fair / 🟩 good / 🟦 mastered / ⬜ unmeasured
- 下次备考时优先出 🟥🟨 概念的题目
- 掌握度数据存 output/progress/，不污染 wiki/ 层的知识内容

---

## Wiki 文章通用 Frontmatter

```yaml
---
title:
domain: <L1>/<L2>
updated: YYYY-MM-DD
sources:
  - raw/xxx.md
tags: []
related: []
visual:
  excalidraw: atlas/<L1>/<L2>/<concept>.excalidraw
  mermaid: true
---
```

---

## Obsidian Markdown 规范

概念页使用 Obsidian Flavored Markdown（需安装 `@obsidian-markdown` skill 或遵循以下内联规范）：

**Callouts** — 标注重要内容：
```markdown
> [!important] Exam Tip
> 这是考试高频考点

> [!warning] Common Mistake
> 学生常见错误
```

**Highlights** — 标注关键术语：`==关键术语==` 在 Obsidian 中显示为高亮

**Wikilinks** — 概念间互相引用：
- `[[concept-name]]` 链接到同 vault 内的其他概念页
- **仅链接已存在的页面**，Lint 操作会检查链接有效性
- 正文中用 wikilink 引用 related 概念，与 frontmatter `related: []` 互补

**Embeds** — 嵌入其他文件内容：
- `![[concept-name#Section]]` 嵌入另一个概念页的特定章节

---

## 依赖 Skills

| Skill | 作用 | 必须 | 降级策略 |
|---|---|---|---|
| `@obsidian-markdown` | Markdown 语法规范 | 推荐 | 按上方内联规范手写 |
| `@obsidian-canvas-creator` | 生成 _map.canvas | 推荐 | 按 Canvas JSON 格式手写 |
| `@excalidraw-diagram` | 生成 .excalidraw 概念关系图 | 推荐 | 跳过，仅保留 wiki 文字 |
| `@mermaid-visualizer` | 生成 mermaid 代码块 | 推荐 | 手写 mermaid 语法 |
| `@obsidian-bases` | 生成 dashboard.base 数据视图 | 可选 | 不影响核心功能 |

**优先级**：wiki 文字内容 > mermaid 嵌入 > canvas 更新 > excalidraw 生成 > bases 视图

非 Claude Code 环境（OpenClawd 等）无法调用 @skill 时，按降级策略执行。核心规范已内联在本文件中，不依赖外部 skill 文件。

---

## 可扩展占位符（由 CLAUDE.md 覆盖）

| 占位符 | 默认值 | 说明 |
|---|---|---|
| `{{language_style}}` | neutral | neutral / technical / student-friendly |
| `{{concept_format}}` | standard | standard / technical / exam-oriented |
| `{{mode}}` | research | research / study / 可组合如 research+study |
| `{{visual_auto}}` | true | true=自动判断 / false=仅明确要求时生成 |

---

## 错误处理

- 数据源无法访问 → 报告，跳过，不中断
- @skill 调用失败 → 按优先级降级：跳过可视化，继续写 wiki 文字，log.md 追加提示
- raw 文件过大 → 自动分段，每段生成独立页面并互链
- 摄入内容不属于已有 domain → 建议新增分类，确认后执行 Domain 操作
- wikilink 目标不存在 → Ingest 时不创建链接，Lint 时报告并建议修复
