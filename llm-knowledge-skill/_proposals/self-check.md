# Stream Layer Implementation — Self Check

执行日期:2026-05-03
分支:`stream-layer`(未提交、未推送)
方案版本:v2(含 reviewer 反馈 13 项 + 终轮 5 项 M1-M5)

## 1. 验收标准核对(对照 v2 §6)

| # | 检查项 | 通过条件 | 实测 | 结果 |
|---|---|---|---|---|
| 1 | skill domain-agnostic | SKILL.md 不出现具体 stream 子类型作为强制配置 | briefs/inbox 等仅作为**示例**(描述句、命令示范、yaml 示例值)出现,不是强制 | ✅ |
| 2 | frontmatter 增量 | 仅新增 `type` + stream 专属字段;Lint 第 5 条按 type 路由 | SKILL.md 第 5 条已分流(line 410-428),stream 走第 8 条 | ✅ |
| 3 | atlas 隔离 | `atlas.*stream` 仅出现否定描述 | 全部 6 处提及均为"不服务"或架构图组件并列(无错误关联) | ✅ |
| 4 | 操作扩展性 | Init / Ingest / Lint / Query 均扩展不删除 | Init 7→8 步、Lint 9→11 步、Ingest 加 Stream 变体、Query 加扩展段 | ✅ |
| 5 | stream 可选性 | Init 第 7 步 + Lint 8a/8b 守卫一致 | Init 第 7 步守卫:"若 CLAUDE.md 含 Stream 配置";Lint 8a 守卫:"stream/ 目录存在";Lint 8b 守卫:"两者任一" | ✅ |
| 6 | stream frontmatter 不被现有 Lint 误报 | 第 5 条按 type 字段路由,stream 文件走第 8 条 | 已实现,有显式注释"由第 8 条专门校验,本条不重复" | ✅ |
| 7 | 双层 → 多层一致 | 全仓库 `双层 / dual-layer / Dual-Layer / two layers` 0 残留 | Grep 返回 exit 1(0 命中) | ✅ |
| 8 | README 一致 | 5 层结构图、Vault Contract、Architecture 段口径一致 | README.md 与 README_CN.md 镜像翻译,均含 5 层架构图 + Vault Contract | ✅ |

## 2. 文件级最终状态

| 文件 | 状态 | 说明 |
|---|---|---|
| `llm-knowledge/SKILL.md` | 改 | 9 处编辑全部应用 |
| `templates/tech-engineer/CLAUDE.md` | 改 | 加 Stream 配置段(briefs/digests/inbox) |
| `templates/_custom/CLAUDE.md` | 改 | 末尾追加可选 Stream 配置(注释包裹) |
| `templates/year8-student/CLAUDE.md` | 不动 | 按计划保持不变 |
| `README.md` | 改 | 标题 + Architecture 段 + Vault Contract |
| `README_CN.md` | 改 | 镜像翻译同上 |
| `_proposals/stream-layer-implementation.md` | 创(v2) | 含 v2 修订与 M1-M5 |
| `_proposals/changelog.md` | 创 | 6 行执行记录 |
| `_proposals/self-check.md` | 创 | 本文件 |

## 3. 已知遗留(不阻塞)

1. **时间窗硬编码**:Stream 变体「过去 7 天」与 Lint 第 9 条「过去 14 天」未下放到 CLAUDE.md。后续可加配置项 `{{stream_window_days}}` 与 `{{stream_lint_window_days}}`。
2. **Stream 沉淀粒度**:当前去重以 wiki 概念页为单位(整个 stream 文件已被任一 wiki 引用即跳过)。若一份周报含多个独立主题,可能漏沉淀部分主题。后续可改为"按主题去重"。
3. **README ASCII 流程图 GitHub 渲染**:5 层架构图采用双列(Sources / Products),等宽字体下对齐良好;Markdown 渲染依赖等宽 code block,已用 ` ``` ` 包裹,无问题。

## 4. 回滚预案

```bash
git checkout master
git branch -D stream-layer
```

(分支未合入、未推送)

## 5. 待用户决定

**git 操作**:本次执行**未提交、未推送**。是否需要:
- (a) 在 stream-layer 分支创建 commit(本地)
- (b) 推送 stream-layer 到 origin
- (c) 创建 PR 合并到 master
- (d) 直接 merge 到 master 并删除分支

请用户指示。
