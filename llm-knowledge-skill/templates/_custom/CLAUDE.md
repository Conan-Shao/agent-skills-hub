# My Knowledge Base

## 加载 Skill
@llm-knowledge

---

## 关于我

<!-- 简单描述你是谁、这个知识库的主要用途 -->

---

## Skill 配置

```
{{language_style}}      = neutral | technical | student-friendly
{{mode}}                = research | study | research+study
{{visual_auto}}         = true | false
{{concept_format}}      = standard | technical | exam-oriented
```

---

## 语言风格

<!-- 描述输出语言和风格偏好 -->

---

## Domain 配置

<!-- 用缩进列表定义你的知识分类，最多三级 -->
<!-- 随时可以通过对话让 Claude 帮你新增或调整 -->

- domain1/ — 描述
  - category1/ — 描述
  - category2/ — 描述
- domain2/ — 描述
  - category1/ — 描述

---

## 概念页模板

<!-- 使用 Skill 默认模板，或在此覆盖 -->

---

## 可视化偏好

- **Mermaid**：<!-- 什么场景用 -->
- **Excalidraw**：<!-- 什么场景用 -->
- **Canvas**：<!-- 什么场景用 -->

---

## Ingest 偏好

<!-- 你主要从哪里获取知识？ -->

---

## 不在范围内

<!-- 不应摄入的内容类型 -->

---

## Stream 配置（可选）

<!--
若你有外部自动化任务定期推送内容到知识库（如每日简报、每周 digest），
在此处声明 stream 子类型。Skill 会按这里的配置创建 stream/<type>/ 目录，
Lint 操作会按此校验 stream 一致性。

不需要此功能可删除整段。
-->

<!-- 示例：
- briefs/  — 每日简报
- digests/ — 周报

文件命名：
- 日级：YYYY-MM-DD.md
- 周级：YYYY-Www.md
-->
