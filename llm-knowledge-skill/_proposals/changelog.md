# Stream Layer Implementation — Changelog

执行日期:2026-05-03
分支:`stream-layer`
方案:`_proposals/stream-layer-implementation.md` (v2)

## 执行记录

- `llm-knowledge/SKILL.md`:9 处编辑完成(#S1 用途 / #S2 原则 2,3,6 / #S3 目录树 / #S4 新增 Stream 流式层段 / #S5 Init 第 7 步 / #S6 Query 扩展 stream / #S7 Ingest Stream 变体 / #S8 Lint 第 5 条按 type 路由 + 新增 8a/8b/9 + 顺延为 11 步 / #S9 通用 Frontmatter)。验证:60 处 stream 提及、0 处 dual-layer 残留。
- `templates/tech-engineer/CLAUDE.md`:#T1 在 Domain 配置之后新增 Stream 配置段(briefs/digests/inbox 三个示例,去掉了 v1 的 english/news 个人化项)。
- `templates/_custom/CLAUDE.md`:#T2 在文件末尾追加可选 Stream 配置段(注释包裹,默认禁用)。
- `templates/year8-student/CLAUDE.md`:不修改。
- `README.md`:#R1(line 3 dual-layer → multi-layer)+ #R2(Architecture 段重写为 5 层)+ #R3(Operations 表后新增 Vault Contract)。验证:0 处 dual-layer / Dual-Layer / two layers 残留。
- `README_CN.md`:#RC1(line 4 双层 → 多层)+ #RC2(架构段重写为 5 层)+ #RC3(操作集后新增「知识库目录契约」段)。验证:0 处「双层」残留(M5 硬约束二次 Grep 通过)。
