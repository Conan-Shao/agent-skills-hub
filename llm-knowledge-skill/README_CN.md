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

## 快速开始

### 第一步：安装可视化 Skills（必须）

```bash
git clone https://github.com/axtonliu/axton-obsidian-visual-skills.git
cp -r axton-obsidian-visual-skills/excalidraw-diagram ~/.claude/skills/
cp -r axton-obsidian-visual-skills/mermaid-visualizer ~/.claude/skills/
cp -r axton-obsidian-visual-skills/obsidian-canvas-creator ~/.claude/skills/
```

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

在 Obsidian 中：**Open folder as vault** → 选择知识库目录

安装 Obsidian 插件：**Excalidraw**（Canvas 是内置的）

### 第五步：初始化并开始使用

```
初始化知识库
摄入 https://你的第一个URL
```

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

## 依赖清单

| 功能 | 依赖 | 必须 |
|---|---|---|
| 文件读写 | Claude Code 内置 | Yes |
| 网页摄入 | Claude Code 内置 web_fetch | Yes |
| Canvas 思维导图 | Axton obsidian-canvas-creator | Yes |
| Excalidraw 关系图 | Axton excalidraw-diagram | Yes |
| Mermaid 流程图 | Axton mermaid-visualizer | Yes |
| Obsidian 可视化 | Obsidian + Excalidraw 插件 | Yes（人类阅读层） |
| GitHub 仓库摄入 | GitHub MCP | Optional |
| PDF 文本提取 | pdftotext / pymupdf | Optional |
| YouTube 字幕 | yt-dlp | Optional |
| 大规模语义检索 | qmd CLI | Optional（>100 篇） |
| 内部文档摄入 | Confluence/GitLab/Jira MCP | Optional |

---

## 致谢

- 方法论：Andrej Karpathy，LLM Wiki（2026-04-03）
- 可视化 Skills：[axtonliu/axton-obsidian-visual-skills](https://github.com/axtonliu/axton-obsidian-visual-skills)
