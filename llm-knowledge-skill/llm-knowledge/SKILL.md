# LLM Knowledge Base Skill

## 用途
构建和维护个人知识库。知识库跟人走，不跟角色或项目走。
采用双层架构：
- **wiki/** — AI 查询层（平铺 Markdown，token 友好）
- **atlas/** — 人类阅读层（Obsidian Canvas / Excalidraw / Mermaid）

---

## 核心设计原则

1. **知识库 = 个人资产**：一个人一个库，domain 随兴趣和成长演化
2. **双层产物**：同一份 raw/ 资料 → 编译为 wiki/（AI 读）+ atlas/（人读）
3. **LLM 是编译器**：raw/ 是源码，LLM 负责全部写入，人类只读
4. **直接文件操作**：Agent 直接写入 Obsidian vault 目录（Claude Code 用 Write 工具，其他 Agent 用对应文件操作能力）
5. **视觉驱动理解**：流程 → Mermaid；概念关系 → Excalidraw；领域全貌 → Canvas
6. **Schema 驱动**：CLAUDE.md 配置语言风格、domain 树、视觉触发规则

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
│   ├── overview.canvas             ← 全库鸟瞰图（L1 节点）
│   ├── <L1>/
│   │   ├── _map.canvas             ← 领域总图（L2 节点）
│   │   └── <L2>/
│   │       ├── _map.canvas         ← 分类地图（topic 节点，链接 wiki 页）
│   │       └── <concept>.excalidraw
│   └── ...
│
└── output/                         ← 查询结果输出
```

---

## 可视化触发规则

| 内容特征 | 触发动作 | Skill |
|---|---|---|
| 包含流程 / 步骤 / 时序 | 嵌入 mermaid 代码块到 wiki 概念页 | `@mermaid-visualizer` |
| 包含多概念关系 / 层级结构 | 生成 atlas/<L1>/<L2>/<concept>.excalidraw | `@excalidraw-diagram` |
| 新增 L1/L2（或执行 Map 操作） | 生成或更新对应 `_map.canvas` | `@obsidian-canvas-creator` |
| 用户明确要求「画图 / 思维导图」 | 按类型选择对应 skill | 三选一 |

**Canvas 节点规范**：每个概念节点的 `file` 字段指向对应 wiki 路径，Obsidian 内点击直接跳转。

---

## 操作集

### Init — 初始化
**触发**：「初始化知识库」

1. 读取 CLAUDE.md 中的 domain 树
2. 创建 raw/、wiki/concepts/、wiki/entities/、atlas/、output/ 目录
3. 按 domain 树创建 wiki/concepts/ 和 atlas/ 的子目录结构
4. 创建 wiki/index.md（含 domain 树导航）、wiki/log.md
5. 创建 atlas/overview.canvas（L1 节点）及各级 `_map.canvas`
6. 若 CLAUDE.md 不存在，提示选择场景模板

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
- 若有流程/步骤 → 调用 `@mermaid-visualizer`，嵌入 mermaid 代码块
- 更新 wiki/entities/（如有实体）
- 更新 wiki/index.md 和 wiki/log.md
- 若 topic 属于尚未存在的 L1/L2 → 先创建目录和 canvas，再更新 CLAUDE.md domain 树

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
2. 检查 atlas/ Canvas 节点是否都有对应 wiki 页（清理无效链接）
3. 验证 CLAUDE.md domain 树与实际目录结构一致
4. 自动修复可确定问题，列出需人工确认的矛盾
5. 追加 log.md

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
3. 创建新的 `_map.canvas`
4. 更新 atlas/overview.canvas 和 wiki/index.md
5. 追加 log.md

---

### Exam — 备考模式
**触发**：「备考 <科目> <日期>」

仅在 `{{mode}}` 包含 `study` 时激活：
1. Read wiki/concepts/<subject>/ 全部页面
2. 按重要程度 × 完整度排序 → Priority Revision List
3. 调用 `@obsidian-canvas-creator` 生成备考 Canvas（高优先级节点高亮）
4. 生成 Marp 复习卡片（正面问题 / 背面答案+记忆技巧）
5. 生成 5 道模拟题（3 选择 + 2 简答）附解析
6. 写入 output/exam-<subject>-<date>/

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
- Axton skill 调用失败 → 跳过可视化，继续写 wiki 文字，log.md 追加提示
- raw 文件过大 → 自动分段，每段生成独立页面并互链
- 摄入内容不属于已有 domain → 建议新增分类，确认后执行 Domain 操作
