# Stream Layer Implementation Plan (v2)

> 状态：**v2 — 已采纳 reviewer 反馈,待最终确认**
> 分支:`stream-layer`
> 适用范围:`llm-knowledge-skill/` 全部内容
>
> **v2 主要修订**:
> - 修 A1:Before/After 全部使用全角冒号(：)与全角括号(()),与现状一致
> - 修 A2:行号按实际 Read 输出修正
> - 修 B3/B5:Lint stream 守卫条件改为"stream/ 不存在 *且* CLAUDE.md 未声明"才跳过
> - 修 C1(关键):新增编辑 #L5,修改 Lint 第 5 条 frontmatter 校验,放行 stream 文件
> - 修 C2:Ingest Stream 变体显式重申 `updated:` 当日写入
> - 修 C4:编辑 #2 同时修订原则 6(Schema 驱动)
> - 修 E3:编辑 #2 同时修订原则 2(双层 → 多层);新增 README/README_CN 的 dual-layer 残留修订
> - 修 F1:Stream 变体步骤 5 给 log 格式示例
> - 修 F2:Lint 第 8 条增加"`type: stream` 必须位于 stream/ 下"
> - 修 F4:Query 操作扩展对 stream 的处理
> - 修 G2:编辑 #4 末尾去掉多余 `---`
> - 修 D2:tech-engineer 模板示例去掉过度个人化的 `english/`

## 0. 决策摘要(不变)

| 决策点 | 结论 |
|---|---|
| stream 与 raw 关系 | 平级,语义边界:raw=手动一次性,stream=自动周期 |
| stream 子目录布局 | `stream/<type>/<year>/<file>.md`,**不设 `_archive/`** |
| stream → wiki 路径 | 单向沉淀,通过 wiki 概念页 `sources:` 反查去重 |
| atlas 是否服务 stream | **否** |
| stream 子类型 | **不进 SKILL.md**,由 CLAUDE.md / 模板配置 |
| frontmatter 变更 | **仅新增 `type` 一个字段** |
| 三个现有操作 | Init / Ingest / Lint **扩展**,不并行 |

---

## 1. 文件修改清单

| # | 文件 | 操作 | 编辑数 |
|---|---|---|---|
| 1 | `llm-knowledge/SKILL.md` | 改 | 9 处 |
| 2 | `templates/tech-engineer/CLAUDE.md` | 改 | 1 处 |
| 3 | `templates/_custom/CLAUDE.md` | 改 | 1 处 |
| 4 | `templates/year8-student/CLAUDE.md` | 不改 | — |
| 5 | `README.md` | 改 | 4 处 |
| 6 | `README_CN.md` | 改 | 4 处 |
| 7 | `_proposals/changelog.md` | 创 | 执行阶段 |
| 8 | `_proposals/self-check.md` | 创 | 阶段 4 |

---

## 2. SKILL.md — 9 处编辑(精确 diff)

> **执行注意**:SKILL.md 全文使用**全角冒号(：)与全角括号(())**。所有 Before/After 必须严格沿用,否则 Edit 工具不匹配。

### 编辑 #S1 — 「用途」段落升级到 5 层

**位置**:line 23-25

**Before**:
```
采用双层架构：
- **wiki/** — AI 查询层（Obsidian Flavored Markdown，token 友好）
- **atlas/** — 人类阅读层（Canvas / Excalidraw / Mermaid / Bases 数据视图）
```

**After**:
```
采用 5 层架构：
- **raw/** — 手动一次性摄入的源料（LLM 只读）
- **stream/** — 外部自动化周期推送的源料（LLM 只读,可选层）
- **wiki/** — AI 查询层（Obsidian Flavored Markdown，token 友好）
- **atlas/** — 人类阅读层（Canvas / Excalidraw / Mermaid / Bases 数据视图）
- **output/** — 查询结果与备考产物
```

---

### 编辑 #S2 — 「核心设计原则」原则 2、3、6 同步修订

**位置**:line 32-36(三条原则一次性替换,确保上下文连贯)

**Before**:
```
2. **双层产物**：同一份 raw/ 资料 → 编译为 wiki/（AI 读）+ atlas/（人读）
3. **LLM 是编译器**：raw/ 是源码，LLM 负责全部写入，人类只读
4. **直接文件操作**：Agent 直接写入 Obsidian vault 目录（Claude Code 用 Write 工具，其他 Agent 用对应文件操作能力）
5. **视觉驱动理解**：流程 → Mermaid；概念关系 → Excalidraw；领域全貌 → Canvas
6. **Schema 驱动**：CLAUDE.md 配置语言风格、domain 树、视觉触发规则
```

**After**:
```
2. **多层产物**：`raw/` 与 `stream/` 是源料层 → 编译为 `wiki/`（AI 读）+ `atlas/`（人读）
3. **LLM 是编译器**：`raw/` 与 `stream/` 是源,`wiki/` 与 `atlas/` 是产物;LLM 负责全部产物写入,人类只读产物
4. **直接文件操作**：Agent 直接写入 Obsidian vault 目录（Claude Code 用 Write 工具，其他 Agent 用对应文件操作能力）
5. **视觉驱动理解**：流程 → Mermaid；概念关系 → Excalidraw；领域全貌 → Canvas
6. **Schema 驱动**：CLAUDE.md 配置语言风格、domain 树、视觉触发规则、stream 子类型（若启用）
```

---

### 编辑 #S3 — 「目录结构约定」目录树

**位置**:line 137-161(整段代码块,不含 `` ``` `` 围栏)

**Before**(line 138-160 的实际目录树内容):
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

**After**:
```
<knowledge-base-root>/              ← 同时是 Obsidian vault 根目录
├── CLAUDE.md                       ← 个人配置（domain 树、语言风格、偏好、stream 子类型）
├── raw/                            ← 手动一次性摄入的源料（只读，LLM 不修改）
│   └── <type>-<topic>-<YYYYMMDD>.md
│
├── stream/                         ← 外部自动化周期推送的源料（可选层，只读）
│   └── <type>/<year>/<file>.md     ← 子类型与命名由 CLAUDE.md 配置
│
├── wiki/                           ← AI 查询层
│   ├── index.md                    ← AI 导航入口（domain 树 + 全部概念页链接）
│   ├── concepts/<L1>/<L2>/         ← 概念页按 domain 层级存放
│   ├── entities/                   ← 实体页（人物、组织、工具等）
│   └── log.md                      ← 操作日志
│
├── atlas/                          ← 人类阅读层（Obsidian 可视化浏览，不服务 stream）
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

### 编辑 #S4 — 新增「Stream 流式层」段落

**位置**:在 line 162 的 `---` **之后**插入,与原 line 163 空行之间。即 stream 段在「目录结构约定」与「可视化触发规则」中间,作为独立 H2 段。

**注意 G2**:不要在新段开头额外加 `---`,因为前一行已有 `---`;新段末尾的 `---` 复用原"可视化触发规则"上方那条(即原 line 162 的 `---` 现在变成本段的开头分隔,我们在末尾再加一个 `---` 接「可视化触发规则」)。最终序列:
```
[原 line 162] ---
[新增] ## Stream 流式层 ... (内容)
[新增] ---
[原 line 163-164] (空行)
[原 line 165] ## 可视化触发规则
```

**新增内容**(完整粘贴,不含外层围栏):

```
## Stream 流式层

`stream/` 容纳由**外部自动化任务**（cron / scheduled agent / 推送脚本）周期生成的源料,例如每日简报、每周新闻、晨读 digest、待整理 inbox。

### 与 raw/ 的语义边界

| 维度 | `raw/` | `stream/` |
|---|---|---|
| 生成方式 | 用户/Agent 手动一次性摄入 | 外部自动化周期推送 |
| 触发频率 | 偶发 | 周期（日/周/月） |
| 文件命名 | `<type>-<topic>-<YYYYMMDD>.md` | `YYYY-MM-DD.md` / `YYYY-Www.md` |
| 路径布局 | `raw/` 平铺 | `stream/<type>/<year>/<file>.md` |
| LLM 写入权限 | 摄入时一次写入,之后只读 | 全程只读 |
| 是否需归档 | 不归档（原始保存） | 不归档（年份目录天然分区） |

### 路径约定

```
stream/<type>/<year>/<file>.md
```

- `<type>` — 子类型,由 CLAUDE.md 的「Stream 配置」段落定义
- `<year>` — 4 位年份,**与文件 frontmatter `date:` 字段年份一致**
- `<file>` — 推荐命名:
  - 日级:`YYYY-MM-DD.md`
  - 周级:`YYYY-Www.md`(W 大写,周数 ISO-8601)

### Frontmatter 标准（stream 文件强制）

```yaml
---
type: stream
stream_type: <type>     # 必须匹配所在 stream/<type>/ 目录名
date: YYYY-MM-DD        # 推送日期或周一日期
tags: []
---
```

### LLM 行为约束

| 操作 | 允许 | 备注 |
|---|---|---|
| 读取 stream 文件 | ✅ | Ingest / Query 均可 |
| 在 wiki 概念页中引用 stream | ✅ | wikilink 或 frontmatter `sources:` |
| 修改 stream 原文 | ❌ | 与 raw/ 一致,只读 |
| 删除 stream 文件 | ❌ | 由用户/外部脚本管理 |
| 在 stream 文件中加"已沉淀"标记 | ❌ | 通过 wiki `sources:` 反查去重 |
| 创建 atlas/ 下的 stream 可视化 | ❌ | atlas 不服务 stream |

### 沉淀路径

stream → wiki 是**单向沉淀**,由 `Ingest 的 stream 变体`触发(详见操作集)。

### 可选性

stream 是**可选层**:不需要周期推送的用户可以不建 stream/ 目录,也不在 CLAUDE.md 声明 Stream 配置。Init 操作根据 CLAUDE.md 是否声明 Stream 配置决定是否创建该目录。Lint 操作根据"目录是否存在 + CLAUDE.md 是否声明"两个条件组合判断校验范围。

```

---

### 编辑 #S5 — 「Init」操作步骤追加第 7 步

**位置**:line 231-241(Init 操作 7 步全部替换为 8 步,确保编号连贯)

**Before**:
```
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
```

**After**:
```
1. 读取 CLAUDE.md 中的 domain 树和 Stream 配置（若有）
2. 创建 raw/、wiki/concepts/、wiki/entities/、atlas/、output/ 目录
3. 按 domain 树创建 wiki/concepts/ 和 atlas/ 的子目录结构
4. 为每个 L1 和 L2 目录创建 hub 文件：
   - **命名规则：文件名 = 目录名**（`academic/` → `academic.md`，`maths/` → `maths.md`）
   - **禁止使用 `_index.md`**：Obsidian 图谱中所有同名文件显示为同一标签，无法区分
   - L1 hub 内容：列出所有 L2 子分类，每项用 `[[L2-hub-name]]` 链接
   - L2 hub 内容：列出所有话题概念页，底部加 `Part of [[L1-hub-name]]`
5. 创建 wiki/index.md（含 domain 树导航）、wiki/log.md
6. 创建 atlas/overview.canvas（L1 节点）及各级 `_map.canvas`
7. **若 CLAUDE.md 含 Stream 配置**：创建 `stream/` 根目录及各 `stream/<type>/` 子目录(年份目录在首次写入时按需创建,不预建)
8. 若 CLAUDE.md 不存在，提示选择场景模板
```

---

### 编辑 #S6 — 「Query」操作扩展 stream 范围

**位置**:line 288-293

**Before**:
```
### Query — 查询
**触发**：直接提问

- **< 100 篇**：Read wiki/index.md → 定位 → Read 相关页 → 回答
- **≥ 100 篇**：qmd query → Read 命中页 → 回答
- 回答 Write 到 output/ 存档
```

**After**:
```
### Query — 查询
**触发**：直接提问

**默认范围**:wiki/(已沉淀知识)。

- **< 100 篇**：Read wiki/index.md → 定位 → Read 相关页 → 回答
- **≥ 100 篇**：qmd query → Read 命中页 → 回答
- 回答 Write 到 output/ 存档

**扩展到 stream/**(若 stream/ 存在):

- 用户问题包含时间限定(如「今天的简报」「本周新闻」「last week」「上周」)→ 加搜 `stream/<type>/<current-year>/` 中匹配日期的文件
- 用户显式要求(如「在 stream 里搜」「翻一下 inbox」)→ 仅搜 `stream/`
- 默认提问不主动搜 stream/(避免噪声;长尾内容应先沉淀到 wiki/ 再被检索)

> 默认不搜 stream/ ≠ 被忽略。Lint 第 9 条会定期报告未沉淀的 stream 文件,提示用户触发 Ingest Stream 变体。
```

---

### 编辑 #S7 — 「Ingest」操作末尾追加 Stream 变体子节

**位置**:在 line 285 (Ingest 输出摘要行)与 line 286 (`---`)之间插入。

**新增内容**(完整粘贴):

```

#### Stream 变体 — 周期源料沉淀

**触发**：「整理 stream」/「沉淀 stream/<path>」/「review last week's briefs」/「整理 inbox」

**与默认 Ingest 的区别**：
- 输入是已存在的 `stream/<type>/<year>/*.md` 文件,不调用 web_fetch / pdftotext 等数据源工具
- **跳过 A 步**(获取),直接从 B 步开始
- **跳过 D 步**(atlas 不服务 stream)
- **不修改 stream 原文**(无"已沉淀"标记;通过 wiki `sources:` 反查去重)

**执行步骤**：

1. **范围确定**:按用户指定的时间窗或路径扫描 stream 文件
   - 「整理 stream」无参数 → 默认扫描过去 7 天所有 stream 文件
   - 「整理 inbox」 → 仅扫描 `stream/inbox/`(若 CLAUDE.md 配置该子类型)
   - 「沉淀 stream/briefs/2026/2026-05-03.md」 → 处理单文件
2. **去重过滤**:对每个候选 stream 文件,Grep `wiki/concepts/**/*.md` 的 frontmatter `sources:` 字段
   - 已被引用 → 跳过(避免重复沉淀)
   - 未被引用 → 进入下一步
3. **B 步(分析)**:同默认 Ingest,提取核心概念、实体、关系、判断 domain
4. **C 步(写 wiki/)**:同默认 Ingest,**沿用 C 步全部要求**(更新或新建概念页、L2 hub 加入 `[[topic]]` 链接、概念页底部加 `Part of [[L2-hub]]`、更新 wiki/index.md、`updated:` 字段写入当日日期 — grace-period 规则依赖);frontmatter `sources:` 必须包含本 stream 文件的相对路径,如:
   ```yaml
   sources:
     - stream/briefs/2026/2026-05-03.md
   ```
5. 更新 wiki/log.md,推荐格式:`[YYYY-MM-DD] stream-ingest: N streams → M wiki concepts (sources: stream/briefs/2026/...)`

**输出摘要**：列出本次沉淀的 stream 源文件清单 + wiki/ 新建/更新的概念页清单。

```

**时间窗硬编码备注**:7 天(本变体)与 14 天(Lint 第 9 条)暂硬编码,后续可下放到 CLAUDE.md 配置,本次不做。

---

### 编辑 #S8 — 「Lint」操作:第 5 条同步修订 + 新增第 8/9 条 + 顺延

**关键修复(C1)**:第 5 条 frontmatter 校验必须**按 `type` 字段路由**,否则 stream 文件会被全部报错。

**位置**:line 305-326(覆盖 Lint 第 5、6、7、8、9 步,改后变为 5、6、7、8、9、10、11 步)

**Before**:
```
5. **Frontmatter 校验**：
   - 必需字段存在：`title`、`domain`、`updated`、`tags`（`tags` 允许空列表 `[]`）
   - `title`、`domain`、`updated` 必须非空；`updated` 为合法 `YYYY-MM-DD` 日期
   - `domain` 必须匹配 CLAUDE.md 中定义的 domain 树
   - `related:` 规范化形式为**裸 YAML 列表**：`related: [concept-a, concept-b]`
     - **错误级别**：`related: [[x, y]]`（双括号+逗号，YAML 解析成嵌套列表，非预期形式）
     - **警告级别**（建议简化，不强制）：`related: ["[[x]]", "[[y]]"]`

6. **章节数合理性检查**：
   - 概念页 `##` 一级章节少于 3 个 → **警告**（"页面可能是 stub"），**hub 文件豁免**
   - Hub 文件定义：文件名（去扩展名）等于父目录名，或等于父目录名去掉前缀 `NN-` 后的部分
     - 例：`academic/academic.md`、`maths/maths.md`、`01-patterns/patterns.md` 均为 hub
   - 概念页 `##` 一级章节 ≥ 10 个 → info 级提示（"页面可能过长，考虑拆分"）

7. **`## My Notes` 死代码检查**：
   - 任意页面含 `## My Notes`（任意层级），且该节只有 HTML 注释或空白 → 警告
   - **宽限期**：`updated` 日期早于 2026-04-22 的文件豁免该警告（让既有旧页自然迭代清理）
   - 警告文案：「可直接删除该段落，仍然可以手工编辑该文件」

8. 自动修复可确定问题，列出需人工确认的矛盾
9. 追加 log.md
```

**After**:
```
5. **Frontmatter 校验**(按 `type` 字段分流):

   **路由**:读取文件 frontmatter 的 `type` 字段。`type` 缺失 → 按文件位置推断:
   - 位于 `wiki/concepts/` 下 → 视为 `knowledge`
   - 位于 `wiki/entities/` 下 → 视为 `entity`
   - 位于 `stream/` 下 → 视为 `stream`(但 stream 文件 `type` 字段实际**强制**,缺失即报错,见第 8 条)
   - 其他位置 → 跳过本条校验

   **`type: knowledge` / `type: entity`(wiki 文件)**:
   - 必需字段存在：`title`、`domain`、`updated`、`tags`（`tags` 允许空列表 `[]`）
   - `title`、`domain`、`updated` 必须非空；`updated` 为合法 `YYYY-MM-DD` 日期
   - `domain` 必须匹配 CLAUDE.md 中定义的 domain 树
   - `related:` 规范化形式为**裸 YAML 列表**：`related: [concept-a, concept-b]`
     - **错误级别**：`related: [[x, y]]`（双括号+逗号，YAML 解析成嵌套列表，非预期形式）
     - **警告级别**（建议简化，不强制）：`related: ["[[x]]", "[[y]]"]`
   - 出现 `type` 字段但值不在 `knowledge | entity | stream | raw` 集合内 → 错误

   **`type: stream`(stream 文件)**:由第 8 条专门校验,本条不重复。

6. **章节数合理性检查**：
   - 概念页 `##` 一级章节少于 3 个 → **警告**（"页面可能是 stub"），**hub 文件豁免**
   - Hub 文件定义：文件名（去扩展名）等于父目录名，或等于父目录名去掉前缀 `NN-` 后的部分
     - 例：`academic/academic.md`、`maths/maths.md`、`01-patterns/patterns.md` 均为 hub
   - 概念页 `##` 一级章节 ≥ 10 个 → info 级提示（"页面可能过长，考虑拆分"）

7. **`## My Notes` 死代码检查**：
   - 任意页面含 `## My Notes`（任意层级），且该节只有 HTML 注释或空白 → 警告
   - **宽限期**：`updated` 日期早于 2026-04-22 的文件豁免该警告（让既有旧页自然迭代清理）
   - 警告文案：「可直接删除该段落，仍然可以手工编辑该文件」

8. **Stream 校验**(分两部分):

   **8a. 强校验 — 守卫:`stream/` 目录存在,否则跳过 8a 全部**
   - **位置一致性**:任何带 `type: stream` frontmatter 的文件**必须位于 `stream/` 目录下**;出现在 wiki/ raw/ atlas/ output/ → 错误
   - **必填 frontmatter**:`type: stream`、`stream_type`、`date`、`tags`(允许 `[]`)
   - `stream_type` 必须等于所在 `stream/<type>/` 路径中的 `<type>`
   - `date` 必须为合法 `YYYY-MM-DD`,且年份必须等于路径中的 `<year>` 段

   **8b. 配置漂移轻校验 — 守卫:`stream/` 目录存在 **或** CLAUDE.md 含 Stream 配置,任一即触发**
   - CLAUDE.md「Stream 配置」声明的子类型 → 若对应 `stream/<type>/` 目录不存在 → info(可能尚未推送过)
   - `stream/<type>/` 目录存在 → 若 CLAUDE.md 未声明该子类型 → warning(配置漂移)
   - 两者皆无 → 跳过 8b

9. **Stream 沉淀状态报告**(守卫:`stream/` 目录存在;info 级):
   - 扫描过去 14 天的 stream 文件
   - 对每个文件 Grep `wiki/concepts/**/*.md` 的 `sources:` 字段
   - 列出**未被任何 wiki 文件引用**的 stream 文件,提示「可能漏沉淀」
   - **不强制**:stream 本身允许"看完即弃",此条仅作信息提示

10. 自动修复可确定问题，列出需人工确认的矛盾
11. 追加 log.md
```

---

### 编辑 #S9 — 「Wiki 文章通用 Frontmatter」改为「通用 Frontmatter」并扩展

**位置**:line 379-394

**Before**:
```
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
```

**After**:
```
## 通用 Frontmatter

### 字段:`type`(全库统一)

所有 markdown 文件可在 frontmatter 声明 `type` 字段,用于机械区分文件类别:

| `type` 值 | 适用位置 | 是否强制 |
|---|---|---|
| `knowledge` | `wiki/concepts/**/*.md` | 可省略(默认值,向后兼容) |
| `entity` | `wiki/entities/*.md` | 可省略(默认值) |
| `stream` | `stream/**/*.md` | **强制** |
| `raw` | `raw/*.md` | 可省略(raw 通常不要求 frontmatter) |

Lint 第 5 条按 `type` 字段(或位置推断)路由到对应校验路径。

### Wiki 概念页 frontmatter

```yaml
---
title:
type: knowledge          # 可省略,默认 knowledge
domain: <L1>/<L2>
updated: YYYY-MM-DD
sources:                 # 引用 raw/ 或 stream/ 的相对路径
  - raw/xxx.md
  - stream/briefs/2026/2026-05-03.md
tags: []
related: []
visual:
  excalidraw: atlas/<L1>/<L2>/<concept>.excalidraw
  mermaid: true
---
```

### Stream 文件 frontmatter

```yaml
---
type: stream
stream_type: briefs      # 必须匹配 stream/<type>/ 目录名
date: 2026-05-03
tags: []
---
```
```

---

## 3. 模板修改

### 编辑 #T1 — `templates/tech-engineer/CLAUDE.md`

**位置**:在「## Domain 配置」段落结束(line 60)与「---」(line 62)之间,作为新 H2 段插入。

**新增内容**:

```
---

## Stream 配置

stream 子类型(本场景示例,可按需增删):

- `briefs/` — 每日技术简报、新闻摘要
- `digests/` — 周报 / 月报
- `inbox/` — 待整理的零散 stream 内容

文件命名:
- 日级:`YYYY-MM-DD.md`(briefs / inbox)
- 周级:`YYYY-Www.md`(digests)

stream 文件由外部自动化任务生成,LLM 只读、可沉淀到 wiki/,不修改原文。
执行 `整理 stream` 或 `整理 inbox` 触发沉淀流程(详见 SKILL.md 的 Ingest Stream 变体)。

```

**说明**(回应 D2):去掉了上一版的 `english/` 与 `news/`(过度个人化),保留三个最通用的 stream 子类型作为示例。

### 编辑 #T2 — `templates/_custom/CLAUDE.md`

已确认末尾结构(line 64-66 是「不在范围内」段,无尾部 `---`)。直接在文件末尾追加新段,段首加 `---` 分隔。

```
---

## Stream 配置(可选)

<!--
若你有外部自动化任务定期推送内容到知识库(如每日简报、每周 digest),
在此处声明 stream 子类型。Skill 会按这里的配置创建 stream/<type>/ 目录,
Lint 操作会按此校验 stream 一致性。

不需要此功能可删除整段。
-->

<!-- 示例:
- briefs/  — 每日简报
- digests/ — 周报

文件命名:
- 日级:YYYY-MM-DD.md
- 周级:YYYY-Www.md
-->
```

### 不改 `templates/year8-student/CLAUDE.md`

理由不变:Year 8 学生场景以教材消费为主,无外部自动推送需求。

---

## 4. README 修改

### 编辑 #R1 — `README.md` line 3:dual-layer → multi-layer

**Before**:
```
A personal knowledge base toolkit powered by Claude Code. Based on the Karpathy LLM Wiki methodology, extended with a dual-layer architecture and visual skills integration.
```

**After**:
```
A personal knowledge base toolkit powered by Claude Code. Based on the Karpathy LLM Wiki methodology, extended with a multi-layer architecture and visual skills integration.
```

### 编辑 #R2 — `README.md` line 9-22:Architecture 段落升级到 5 层

**Before**:
```
## Dual-Layer Architecture

```
Same raw/ source material
        ↓
  LLM compiles into two layers
        ↓
wiki/   ← AI query layer (flat Markdown, token-friendly, for LLM retrieval)
atlas/  ← Human reading layer (Canvas + Excalidraw + Mermaid, visual browsing in Obsidian)
```

- **wiki/** optimized for AI: flat structure, index.md navigation, efficient for LLM Read
- **atlas/** optimized for humans: hierarchical directories, clickable Canvas mind maps, Excalidraw diagrams
```

**After**:
```
## Architecture (5 layers)

```
Sources                            Products
─────────                          ────────
raw/      manual ingestion    ┐
                              ├──► wiki/   ← AI query layer
stream/   automated push      ┘    atlas/  ← Human reading layer
                                   output/ ← Query results & exam artifacts
```

- **raw/** — manual one-shot ingestion (URLs, PDFs, screenshots). LLM read-only after ingest.
- **stream/** — *optional layer*. Externally pushed periodic content (daily briefs, weekly digests, inbox). LLM read-only.
- **wiki/** — AI query layer. Flat Markdown, token-friendly, with index.md navigation.
- **atlas/** — Human reading layer. Canvas / Excalidraw / Mermaid / Bases for visual browsing. Does not serve stream/.
- **output/** — Query results and study-mode artifacts (exam packages, progress tracking).
```

### 编辑 #R3 — `README.md` 在 Operations 表后(line 193 后)、`---` 前插入 Vault Contract

**新增内容**:
```

---

## Vault Contract

After installing this skill, the host vault is expected to have this top-level structure:

```
<vault-root>/
├── CLAUDE.md     ← personal config (domain tree, stream subtypes, preferences)
├── raw/          ← manual ingestion sources (LLM read-only)
├── stream/       ← optional: automated periodic push sources (LLM read-only)
├── wiki/         ← AI query layer (LLM writes here)
├── atlas/        ← human visualization layer (LLM writes here)
└── output/       ← query results and exam artifacts
```

**stream/ is optional**. If your CLAUDE.md does not declare a Stream Configuration section, the skill skips stream creation and Lint stream checks. Add stream subtypes to your CLAUDE.md if you have external automation pushing periodic content (cron jobs, scheduled agents, daily/weekly digests).

The `Init` operation creates the layout based on CLAUDE.md. The `Lint` operation validates that the actual layout matches what CLAUDE.md declares.

```

### 编辑 #R4 — `README.md` Compatibility 表中"dual-layer"提法核验

执行时 Grep `dual-layer | Dual-Layer` 全文确认无残留(本次只发现 line 3 与 line 9 两处,均已覆盖)。

---

### 编辑 #RC1-RC4 — `README_CN.md` 同步

定位 4 处需改:
- `README_CN.md:4` — 「扩展双层架构 + 可视化技能集成。」 → 「扩展多层架构 + 可视化技能集成。」
- `README_CN.md:10` — 「## 双层架构」 → 「## 架构(5 层)」+ 段落同步翻译 R2
- 操作表格之后插入「Vault Contract / 知识库目录契约」中文段落(对应 R3)
- Compatibility 段落和其他位置全文 Grep 「双层」并确认是否需要替换

具体翻译在执行阶段按 R1-R3 镜像。

---

## 5. 执行顺序

1. SKILL.md:#S1 → #S9(共 9 处)
2. tech-engineer/CLAUDE.md:#T1
3. _custom/CLAUDE.md:Read → #T2
4. README.md:#R1 → #R4
5. README_CN.md:**硬约束** — 执行前先 Read 完整文件 + Grep `双层` 全文确认所有出现位置,镜像翻译 R1/R2/R3,翻译完后 Grep `双层` 二次验收(应为 0 命中)
6. 写 `_proposals/changelog.md`(每文件改后追加一行)
7. 写 `_proposals/self-check.md`
8. **不提交、不推送**(等用户最终确认)

---

## 6. 验收标准

| 检查项 | 通过条件 |
|---|---|
| skill domain-agnostic | SKILL.md 不出现具体 stream 子类型字面(briefs/news/inbox 等) |
| frontmatter 增量 | 仅新增 `type` 与 stream 专属字段;Lint 第 5 条按 type 路由 |
| atlas 隔离 | Grep `atlas.*stream` / `stream.*atlas`,仅出现"atlas 不服务 stream"这种**否定性**描述 |
| 操作扩展性 | Init / Ingest / Lint / Query 均**新增/修订**条目,未删除既有步骤 |
| stream 可选性 | Init 第 7 步 + Lint 第 8/9 步守卫一致;子类型双向校验不被守卫吞掉 |
| stream frontmatter 不被现有 Lint 误报 | Lint 第 5 条按 type 字段路由,stream 文件走第 8 条 |
| 双层 → 多层一致 | Grep 全仓库 `双层 | dual-layer | Dual-Layer`,无残留 |
| README 一致 | README 5 层结构图、Vault Contract、Architecture 段落口径一致 |

---

## 7. 风险与回滚

| 风险 | 缓解 |
|---|---|
| Lint 编号顺延导致旧引用失效 | 全文 Grep `Lint.*第 [0-9]+ 步`,无外部引用,安全 |
| `type` 字段与现有 wiki 文件兼容性 | Lint 第 5 条新增"出现 `type` 字段但值非法 → 错误";现有文件无 `type` 字段 → 走位置推断,完全兼容 |
| stream 子类型一致性双向校验过严 | 「目录存在但未声明」为 warning(不阻塞),「声明但目录不存在」为 info |
| 中英文标点(全角/半角冒号)Edit 工具不匹配 | v2 已统一用全角,执行时再 Read 一次确认 |
| 嵌套 markdown 代码块(yaml 在 markdown 内)被误剥离 | 执行 Edit 时使用 Write 整段替换,而非 Edit 单行 |

**回滚**:`git checkout master && git branch -D stream-layer`,无任何已合并改动。

---

## 8. 给 reviewer 的最后确认问题

请就以下 3 点确认或拍板:

1. **Q1**:Lint 第 8 条最末一句"子类型一致性双向校验**始终运行**"是否清晰?是否需要再分两小节(「守卫覆盖的检查」vs「始终运行的检查」)以避免误解?
2. **Q2**:Query 操作扩展(#S6)默认不搜 stream/,需要时间限定关键词或显式要求才加搜——这是否过于保守?用户可能期望 stream 也是默认搜索范围。
3. **Q3**:README_CN 翻译需要在执行时再 Read,本方案没逐字给出 After 内容(只列变更要点)。是否要求执行人逐字给出后再写文件,还是允许镜像翻译?

reviewer 答完即可执行。

---

**END OF v2 PLAN**
