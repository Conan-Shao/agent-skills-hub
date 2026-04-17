# My Knowledge Base — 起点：测试开发工程师

## 加载 Skill
@llm-knowledge

---

## 关于我

测试开发工程师，关注测试工程、软件开发、AI/LLM。
同时对行业趋势、投资、生活方式和个人成长都有兴趣。
这是我的个人知识库，会随着兴趣和成长持续扩展。

---

## Skill 配置

```
{{language_style}}      = technical
{{mode}}                = research
{{visual_auto}}         = true
{{concept_format}}      = technical
```

---

## 语言风格

- 中英文混合，技术术语保英文（RAG、MCP、smart contract）
- 默认读者有技术背景，不需要解释常见缩写
- 代码示例用真实可运行片段，非伪代码
- 保持准确，不为「友好」牺牲精确性

---

## Domain 配置

- tech/ — 技术
  - cs/ — 数据结构、算法、OS、网络
  - testing/ — 测试策略、框架、自动化
  - dev/ — 架构、设计模式、语言
  - ai/ — LLM 应用、Agent 开发、Prompt Engineering
  - tools/ — 开发工具、效率工具
- industry/ — 行业
  - ai/ — 人工智能行业格局、趋势
  - embodied-ai/ — 具身智能
  - space/ — 商业航天
  - web3/ — DeFi、智能合约、链上安全
- investment/ — 投资
  - finance/ — 金融市场、投资策略
  - regional/ — 地区发展、政策
  - identity/ — 身份规划、投资移民
- lifestyle/ — 生活
  - food/ — 美食、食谱
  - fitness/ — 运动、健康
  - travel/ — 旅行
- growth/ — 成长教育
  - english/ — 英语学习
  - education/ — 教育规划
  - career/ — 职业发展

---

## 概念页模板（technical 格式）

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

## 定义
<!-- 1-3 句准确定义，保留关键英文术语 -->

## 核心机制
<!-- 工作原理，可含代码片段或架构描述 -->

## 与测试工程的关联
<!-- 可选。这个概念如何影响测试策略？ -->

## 实际应用场景

## 常见坑 / 注意事项

## 参考资源

## Related
```

---

## 可视化偏好

- **Mermaid**：架构流程、请求生命周期、测试流程 → 嵌入概念页
- **Excalidraw**：多组件关系图、系统依赖图 → 存 atlas/
- **Canvas**：每个 L2 分类一张地图

---

## Ingest 偏好

优先：官方技术文档、arXiv 论文、GitHub 高质量开源项目
次优：知名技术博客（有署名的深度文章）、YouTube 技术演讲（字幕）

摄入行业资讯时额外提取：关键趋势、竞争格局、对测试/开发工程的影响
摄入 AI/LLM 论文时额外提取：核心方法、对 AI 测试的启示

---

## 不在范围内

市场行情、价格分析、质量存疑的无署名资料
