# LLM Knowledge Skill

基于 Karpathy LLM Wiki 方法论的个人知识库工具包，由 Claude Code 驱动。
扩展双层架构 + 可视化技能集成。

**核心理念：知识库跟人走，不跟角色或项目走。** 从一个匹配当前阶段的模板出发，随兴趣和成长持续扩展。

---

## 双层架构

```
同一份 raw/ 原始资料
        ↓
  LLM 编译为两层
        ↓
wiki/   ← AI 查询层（平铺 Markdown，token 友好，供 LLM 检索）
atlas/  ← 人类阅读层（Canvas + Excalidraw + Mermaid，Obsidian 可视化浏览）
```

- **wiki/** 对 AI 优化：平铺结构，index.md 导航，适合 LLM 高效 Read
- **atlas/** 对人优化：层级目录，Canvas 思维导图可点击跳转，Excalidraw 关系图帮助理解

---

## Domain 三级层级

知识按三级树形分类，在 CLAUDE.md 中定义：

```
L1: Domain（领域）    → academic, tech, lifestyle ...
L2: Category（分类）  → math, science, testing ...
L3: Topic（主题）     → 概念页文件名（不建子目录）
```

示例（测试开发工程师起点）：
```markdown
- tech/ — 技术
  - cs/ — 数据结构、算法、OS、网络
  - testing/ — 测试策略、框架、自动化
  - dev/ — 架构、设计模式、语言
- industry/ — 行业
  - ai/ — 人工智能行业格局、趋势
  - web3/ — DeFi、智能合约
- investment/ — 投资
  - finance/ — 金融市场、策略
- lifestyle/ — 生活
  - food/ — 美食、食谱
  - fitness/ — 运动、健康
- growth/ — 成长教育
  - english/ — 英语学习
  - career/ — 职业发展
```

随时可以通过对话让 Claude 帮你新增或调整分类。

---

## 快速开始 — Claude Code

### 第一步：安装依赖 Skills

**可视化 Skills（推荐）：**
```bash
git clone https://github.com/axtonliu/axton-obsidian-visual-skills.git
cp -r axton-obsidian-visual-skills/excalidraw-diagram ~/.claude/skills/
cp -r axton-obsidian-visual-skills/mermaid-visualizer ~/.claude/skills/
cp -r axton-obsidian-visual-skills/obsidian-canvas-creator ~/.claude/skills/
```

**Obsidian Markdown & Bases（推荐）：**
```bash
git clone https://github.com/kepano/obsidian-skills.git
cp -r obsidian-skills/skills/obsidian-markdown ~/.claude/skills/
cp -r obsidian-skills/skills/obsidian-bases ~/.claude/skills/
```

**Tutor Skills（可选，用于备考模式）：**
```bash
git clone https://github.com/bevibing/tutor-skills.git
cp -r tutor-skills/skills/tutor ~/.claude/skills/
```

> 所有依赖 Skills 均为**推荐但非必须**。核心 SKILL.md 已内联所有格式规范，不安装任何外部 Skill 也能正常工作。

### 第二步：安装本 Skill

```bash
mkdir -p ~/.claude/skills/llm-knowledge
cp llm-knowledge/SKILL.md ~/.claude/skills/llm-knowledge/
```

### 第三步：创建你的知识库

```bash
# 选一个匹配你当前阶段的模板
mkdir ~/my-knowledge-base && cd ~/my-knowledge-base

# 方案 A：测试开发工程师起点
cp /path/to/templates/tech-engineer/CLAUDE.md ./CLAUDE.md

# 方案 B：Year 8 学生起点
cp /path/to/templates/year8-student/CLAUDE.md ./CLAUDE.md

# 方案 C：从零开始
cp /path/to/templates/_custom/CLAUDE.md ./CLAUDE.md
```

模板只是起点。复制后打开 Claude Code 自由定制：
```
帮我加一个 music 分类到 lifestyle 下面
把 cs/ 从 tech 挪到顶级 domain
```

### 第四步：设置 Obsidian

在 Obsidian 中：**Open folder as vault** → 选择知识库目录。

安装所需插件（详见下方 [Obsidian 插件](#obsidian-插件) 小节）。最关键的是安装 **Excalidraw** 社区插件 —— 不装的话 `.excalidraw` 文件只会显示成一堆 JSON 原文。

### 第五步：初始化并开始使用

```
初始化知识库
摄入 https://你的第一个URL
```

---

## 快速开始 — OpenClawd / 其他 Agent

适用于不支持 Claude Code `~/.claude/skills/` 目录的 Agent（OpenClawd、自定义 LLM Agent 等）：

### 第一步：创建知识库

```bash
mkdir ~/my-knowledge-base && cd ~/my-knowledge-base

# 复制模板作为配置
cp /path/to/templates/tech-engineer/CLAUDE.md ./CLAUDE.md
```

### 第二步：加载 SKILL.md 到 Agent

**方案 A — 写入 System Prompt（推荐）：**
将 `llm-knowledge/SKILL.md` 的完整内容粘贴到 Agent 的 system prompt 或上下文窗口。

**方案 B — 文件加载：**
如果 Agent 能读取本地文件，将 SKILL.md 放到知识库根目录：
```bash
cp llm-knowledge/SKILL.md ~/my-knowledge-base/SKILL.md
```
然后指示 Agent 启动时读取。

### 第三步：依赖 Skills 处理

外部 `@skill` 调用（如 `@obsidian-canvas-creator`）在 Claude Code 之外无法工作。SKILL.md 已内联降级规范：

| 功能 | 处理方式 |
|---|---|
| wiki/（概念页、索引、日志） | 完整支持——纯 markdown 读写 |
| Mermaid 图表 | 支持——Agent 直接写 mermaid 代码块 |
| Canvas（_map.canvas） | Agent 按 SKILL.md 中的规范直接写 Canvas JSON |
| Excalidraw 关系图 | 跳过——仅生成 wiki 文字，后续在 Claude Code 中补充 |
| Obsidian Bases（dashboard.base） | 跳过——使用 wiki/index.md 导航 |

### 第四步：设置 Obsidian

与 Claude Code 路径相同：将知识库目录作为 Obsidian vault 打开，并按下方 [Obsidian 插件](#obsidian-插件) 小节安装插件。

### 第五步：初始化并开始使用

告诉 Agent：`初始化知识库`

---

## 操作集

| 命令 | 触发 | 说明 |
|---|---|---|
| **Init** | 「初始化知识库」 | 按 domain 树创建目录结构 |
| **Ingest** | 「摄入 \<URL\>」 | 获取 → 分析 → 写 wiki/ + atlas/ |
| **Query** | 直接提问 | 检索 wiki → 回答 → 存档到 output/ |
| **Lint** | 「lint」/「health check」 | 检查矛盾、孤立页、断链 |
| **Map** | 「map \<领域\>」 | 重新生成领域 Canvas |
| **Domain** | 「添加分类 \<名称\>」 | 新增/调整 domain 树 |
| **Exam** | 「备考 \<科目\> \<日期\>」 | study 模式：复习清单、卡片、模拟题 |

---

## 支持的数据源

| 输入 | 工具 |
|---|---|
| 网页（https://） | web_fetch |
| GitHub 仓库 | GitHub MCP |
| YouTube 视频 | yt-dlp（字幕） |
| arXiv 论文 | web_fetch |
| PDF 文件 | pdftotext / pymupdf |
| 图片 / 手写笔记 | Claude Vision |
| Confluence 页面 | Confluence MCP |
| Jira issue | Jira MCP |
| GitLab 仓库 | GitLab MCP |

---

## 可视化 Skills 分工

| Skill | 触发时机 | 输出 |
|---|---|---|
| obsidian-canvas-creator | 新增 L1/L2、Map 命令、备考 | `atlas/<L1>/<L2>/_map.canvas` |
| excalidraw-diagram | 概念有多节点关系 | `atlas/<L1>/<L2>/<concept>.excalidraw` |
| mermaid-visualizer | 流程 / 步骤 / 时序 | 嵌入 wiki 概念页 |

---

## 其他 Agent 兼容性

本 Skill 为 Claude Code 设计，但核心逻辑（SKILL.md + CLAUDE.md）适用于任何能读 markdown prompt + 写文件的 LLM Agent。

| 层 | Claude Code | 其他 Agent（OpenClawd 等） |
|---|---|---|
| **wiki/**（AI 层） | 完整支持 | 完整支持——任何 Agent 都能读写 markdown |
| **atlas/**（人类层） | 通过 @skills 完整支持 | 部分支持——需内联可视化格式规范或跳过 |
| **Domain 管理** | 交互式对话 | Agent 读取 CLAUDE.md 配置，按 SKILL.md 指令执行 |

**在其他 Agent 中使用**：
1. 将 SKILL.md 内容粘贴到 Agent 的 system prompt 或上下文
2. 将 CLAUDE.md 放在知识库根目录（Agent 作为配置读取）
3. atlas/ 生成：内联 Canvas/Excalidraw 格式规范，或仅运行 wiki/ 模式，atlas/ 在 Claude Code 中另外生成

---

## 包含文件

```
llm-knowledge-skill/
├── llm-knowledge/
│   └── SKILL.md                    ← Skill 引擎（所有人共用）
├── templates/
│   ├── tech-engineer/CLAUDE.md     ← 起点：测试开发工程师
│   ├── year8-student/CLAUDE.md     ← 起点：Year 8 学生
│   └── _custom/CLAUDE.md           ← 空白模板
├── README.md
├── README_CN.md
└── LICENSE
```

---

## Obsidian 插件

`atlas/` 层生成的文件依赖 Obsidian 插件渲染。不装的话，部分文件只会显示原始 JSON 或纯文本。

### 先关闭安全模式（Restricted Mode）

Obsidian 默认开启 **安全模式 / Restricted Mode**，社区插件市场和已安装插件都不可用。必须先关闭：

1. Obsidian → **Settings（⚙️）** → **Community plugins（第三方插件）**
2. 点击 **Turn on community plugins**（新版为 **Turn off Restricted Mode / 关闭安全模式**）
3. 弹出安全警告 → 确认继续
4. **Browse（浏览）** 按钮被激活，即可进入第三方插件市场

### 必装

**Excalidraw**（社区插件，作者 Zsolt Viczián）

渲染 `.excalidraw` 文件（概念关系图、可视化周期表等）必须装。不装就是一坨 JSON 原文。

安装步骤：
1. **Community plugins** → **Browse** → 搜索 `Excalidraw` → **Install** → **Enable**
2.（可选，推荐）命令面板执行 `Excalidraw: Convert *.excalidraw files to *.excalidraw.md` —— 把文件包装为 markdown 容器，wikilink、frontmatter、搜索才能正常工作。

装不了的备选：打开 [excalidraw.com](https://excalidraw.com)，用 **Open** 载入 `.excalidraw` 文件。只能只读浏览，无法在 Obsidian 里跳转。

### 内置（无需安装）

| 插件 | 状态 | 渲染的内容 |
|---|---|---|
| **Canvas** | 核心插件（Obsidian 1.1+，默认开启） | `.canvas` 文件 —— `_map.canvas` 领域地图、`overview.canvas` 鸟瞰图 |
| **Bases** | 核心插件（Obsidian 1.9+，Beta） | `.base` 文件 —— `dashboard.base` 数据视图。若 Obsidian 版本较老或 Bases 被禁用：Settings → Core plugins → 启用 **Bases** |
| **Mermaid** | 原生 markdown 支持 | 嵌入 wiki 概念页的 ```` ```mermaid ```` 代码块 |
| **MathJax / LaTeX** | 原生 markdown 支持 | 概念页里的 `$…$` 行内公式和 `$$…$$` 块公式 |

### 渲染增强（推荐，Browse 安装）

这些插件能显著提升 `atlas/` 和 `wiki/` 的视觉效果，超越 Obsidian 默认渲染：

| 插件 | 为什么装 |
|---|---|
| **Style Settings** | 绝大多数优秀主题（Minimal、Things、Blue Topaz）的前置依赖。暴露细粒度 CSS 变量（字体大小、间距、callout 颜色）可调 |
| **Iconize**（原名 *Icon Folder*） | 给文件/文件夹加图标 —— 让 domain 树一眼能扫（比如 📘 挂在 `wiki/concepts/academic/`、🧪 挂在 `science/`） |
| **Advanced Tables** | 自动格式化 + 快捷键导航大 markdown 表。`vocab/` 和 `expressions/` 页面大量依赖表格，没这个插件编辑起来很痛苦 |
| **Image Toolkit** | 图片、mermaid / excalidraw 预览点击放大。atlas 图密时很有用 |
| **Highlightr** | 支持多色高亮（不止默认 `==黄色==`）。按单词掌握度或概念类型分色很好用 |

### 生产力插件（可选）

严格来说不属于"渲染"，但契合本知识库的使用流：

| 插件 | 作用 |
|---|---|
| **Dataview** | 按 frontmatter（tags、domain、updated）查询 wiki 生成内联表格。老版本 Obsidian 上可替代 Bases |
| **Templater** | 模板引擎 —— 自己手动建概念页时有用（不通过 Claude） |
| **Tag Wrangler** | 重命名、合并、浏览 vault 内所有 tag（长期积累会有很多 tag） |
| **Outliner** | 让 CLAUDE.md 里的 domain 树缩进列表编辑更顺手 |
| **Linter** | 保存时自动格式化 markdown（尾部空白、标题间距、YAML frontmatter） |

### 主题

默认主题够用，但长概念页的可读性可以更好：

- **Minimal**（作者 kepano）—— 简洁、排版导向，配合 **Style Settings** 可精调
- **Things** —— 柔和配色，适合 atlas 浏览

通过 **Settings → Appearance → Themes → Manage → Browse** 安装。

---

## 依赖清单

### Claude Code Skills

| Skill | 来源 | 级别 | 降级策略 |
|---|---|---|---|
| obsidian-canvas-creator | [axtonliu/axton-obsidian-visual-skills](https://github.com/axtonliu/axton-obsidian-visual-skills) | 推荐 | 按规范手写 Canvas JSON |
| excalidraw-diagram | [axtonliu/axton-obsidian-visual-skills](https://github.com/axtonliu/axton-obsidian-visual-skills) | 推荐 | 跳过，仅保留 wiki 文字 |
| mermaid-visualizer | [axtonliu/axton-obsidian-visual-skills](https://github.com/axtonliu/axton-obsidian-visual-skills) | 推荐 | 按规范手写 mermaid 语法 |
| obsidian-markdown | [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills) | 推荐 | 按 SKILL.md 内联规范手写 |
| obsidian-bases | [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills) | 可选 | 不生成 dashboard.base，用 index.md |
| tutor | [bevibing/tutor-skills](https://github.com/bevibing/tutor-skills) | 可选 | 内置 Exam 模式（无交互追踪） |

### 运行时工具

| 功能 | 工具 | 必须 |
|---|---|---|
| 文件读写 | Claude Code 内置 | Yes |
| 网页摄入 | Claude Code 内置 web_fetch | Yes |
| Obsidian 可视化 | Obsidian + Excalidraw 插件（详见 [Obsidian 插件](#obsidian-插件)） | Yes（人类阅读层） |
| GitHub 仓库摄入 | GitHub MCP | Optional |
| PDF 文本提取 | pdftotext / pymupdf | Optional |
| YouTube 字幕 | yt-dlp | Optional |
| 大规模语义检索 | qmd CLI | Optional（>100 篇） |
| 内部文档摄入 | Confluence/GitLab/Jira MCP | Optional |

---

## 致谢

- 方法论：Andrej Karpathy，LLM Wiki（2026-04-03）
- 可视化 Skills：[axtonliu/axton-obsidian-visual-skills](https://github.com/axtonliu/axton-obsidian-visual-skills)
- Obsidian Skills：[kepano/obsidian-skills](https://github.com/kepano/obsidian-skills)
- Tutor Skills：[bevibing/tutor-skills](https://github.com/bevibing/tutor-skills)
