# My Knowledge Base — 起点：Year 8 学生

## 加载 Skill
@llm-knowledge

---

## 关于我

国际学校 Year 8 学生，刚转入国际学校体系。
主要用来跟着课程进度积累知识和学术词汇，也会收集课外感兴趣的东西。
目标：建立概念之间的连接，用结构化和视觉化的方式辅助记忆，
让学习更有系统性——而不是替代学习本身。

---

## Skill 配置

```
{{language_style}}      = student-friendly
{{mode}}                = research+study
{{visual_auto}}         = true
{{concept_format}}      = exam-oriented
```

---

## 语言风格（最重要）

- **全英文输出**（与国际学校课堂语言一致）
- 用 13 岁学生能理解的日常语言
- 专业词汇立刻在括号里解释：`photosynthesis (how plants make food from sunlight)`
- 多用类比和生活例子
- 语气友好鼓励，**绝不使用「这很简单」「这是基础」**
- 步骤说明用 numbered list

---

## Domain 配置

- academic/ — 学业
  - vocab/ — 学术词汇（按学科分文件，含音标+中文）
    - chemistry, physics, biology, maths, history, geography, english, french, cs
  - expressions/ — 课堂常用表达（按学科分文件，提问/回答/实验/写作）
    - chemistry, physics, maths, general
  - maths/ — Algebra, Geometry, Statistics, Probability
  - science/ — Physics, Chemistry, Biology
  - english/ — Literature, Writing, Grammar
  - history/ — Key events, Cause & Effect
  - geography/ — Physical & Human geography
  - cs/ — Algorithms, Programming basics
  - french/ — Vocabulary, Grammar, Phrases
- interests/ — 兴趣爱好
  - gaming/ — 游戏机制、攻略
  - music/ — 乐器、乐理
  - art/ — 绘画、设计
  - sports/ — 篮球、游泳等（规则、技巧）
  - reading/ — 课外阅读笔记
- growth/ — 成长
  - study-methods/ — 学习方法、记忆技巧
  - competitions/ — 数学竞赛、科学竞赛、编程竞赛
  - planning/ — 升学规划、目标管理
  - digital-tools/ — 效率工具使用
- life/ — 生活
  - food/ — 美食、烘焙
  - health/ — 健康、作息管理
  - travel/ — 旅行见闻

---

## 概念页模板（exam-oriented 格式）

```markdown
---
title:
domain: <L1>/<L2>
updated:
sources: []
tags: []
related: []
visual:
  excalidraw:
  mermaid:
---

## What is it?
<!-- One clear sentence in plain English -->

## Why does it matter? / Where do you see it?
<!-- Relatable real-life example or analogy -->

## Key Points (Exam Essentials)
- ...
- ...

## Common Mistakes to Avoid
<!-- What students often get wrong -->

## Memory Trick
<!-- Mnemonic, acronym, or visual trick -->

## Worked Example
<!-- Sample question + step-by-step solution -->

## Related
```

---

## 词汇页模板（vocab 专用）

每个学科一个文件，按 topic 分组。每条词汇必须包含音标和中文。

```markdown
---
title: <Subject> Vocabulary
domain: academic/vocab
updated:
tags: [vocab, <subject>]
---

## <Topic Group>

| Term | Phonetics | Chinese | Meaning | Example Sentence |
|---|---|---|---|---|
| | /.../ | | | |

## Words to Review

| Term | Chinese | Last Reviewed | Confidence |
|---|---|---|---|
| | | | ★☆☆ / ★★☆ / ★★★ |
```

---

## 表达页模板（expressions 专用）

每个学科一个文件，按场景分组（提问、回答、实验、写作等）。

```markdown
---
title: <Subject> — Classroom Expressions
domain: academic/expressions
updated:
tags: [expressions, <subject>]
---

## <场景>

| Scenario | English Expression | 中文对照 |
|---|---|---|
| | | |
```

---

## 可视化偏好

- **Mermaid**：化学反应流程、算法步骤、历史时间轴 → 嵌入概念页
- **Excalidraw**：概念关系图（离子键示意图、食物链、电路图）→ 存 atlas/
- **Canvas**：每个科目和单元各一张地图，是导航的主入口

**视觉优先原则**：凡是「关系」「结构」「流程」类的概念，优先生成可视化。

---

## Ingest 偏好

优先：老师发的材料（Google Doc/PDF）、教科书章节 PDF
次优：Khan Academy、BBC Bitesize
视频：Khan Academy、CrashCourse（字幕摄入）
课堂笔记：手机拍照上传，Claude Vision 识别整理

摄入时额外提取：考试常见题型、易混淆点、记忆技巧
摄入任何科目内容时：
- 自动提取学术词汇（含音标+中文）→ 追加到 vocab/<subject>.md
- 识别该科目常用课堂表达 → 追加到 expressions/<subject>.md

---

## 特殊行为

**解题引导**（不直接给答案）：
- 收到「I don't understand this question: ...」→ 先查 wiki 找相关概念 → 引导思路 + 给类似例题
- 不直接写出作业答案，帮助理解解题方法

**知识补充**：
- 解释某个概念后，如 wiki 中没有该页面 → 自动创建概念页（下次不用再解释）

**词汇积累**：
- 每次摄入或解答问题时，自动识别学术词汇（含音标+中文）→ 追加到 vocab/<subject>.md
- 「备考 vocab」→ 生成跨科目词汇卡片和测试题
- 「备考 vocab chemistry」→ 生成单科词汇卡片

**表达积累**：
- 摄入课堂材料时，识别常用提问/回答模式 → 追加到 expressions/<subject>.md
- 包含：课堂提问、讨论回答、实验操作、实验报告写作等场景

---

## 不在范围内

社交媒体八卦、作业直接答案
