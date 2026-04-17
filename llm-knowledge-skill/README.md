# LLM Knowledge Skill

A personal knowledge base toolkit powered by Claude Code. Based on the Karpathy LLM Wiki methodology, extended with a dual-layer architecture and visual skills integration.

**Core philosophy: your knowledge base follows you, not your role or project.** It starts from a template matching your current stage, then grows with your interests and career over time.

---

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

---

## Domain Hierarchy (up to 3 levels)

Knowledge is organized in a 3-level tree, defined in your CLAUDE.md:

```
L1: Domain    → academic, tech, lifestyle ...
L2: Category  → math, science, testing ...
L3: Topic     → concept page filenames (no subdirectory)
```

Example (tech engineer starting point):
```markdown
- tech/ — Technology
  - cs/ — Data structures, Algorithms, OS, Networking
  - testing/ — Test strategy, Frameworks, Automation
  - dev/ — Architecture, Patterns, Languages
- industry/ — Industry
  - ai/ — AI landscape, Trends
  - web3/ — DeFi, Smart contracts
- investment/ — Investment
  - finance/ — Markets, Strategy
- lifestyle/ — Life
  - food/ — Recipes, Restaurants
  - fitness/ — Sports, Health
- growth/ — Personal Growth
  - english/ — English learning
  - career/ — Career development
```

You can add or reorganize domains anytime by talking to Claude.

---

## Quick Start

### Step 1: Install Dependency Skills

**Visual Skills (recommended):**
```bash
git clone https://github.com/axtonliu/axton-obsidian-visual-skills.git
cp -r axton-obsidian-visual-skills/excalidraw-diagram ~/.claude/skills/
cp -r axton-obsidian-visual-skills/mermaid-visualizer ~/.claude/skills/
cp -r axton-obsidian-visual-skills/obsidian-canvas-creator ~/.claude/skills/
```

**Obsidian Markdown & Bases (recommended):**
```bash
git clone https://github.com/kepano/obsidian-skills.git
cp -r obsidian-skills/skills/obsidian-markdown ~/.claude/skills/
cp -r obsidian-skills/skills/obsidian-bases ~/.claude/skills/
```

**Tutor Skills (optional, for study mode):**
```bash
git clone https://github.com/bevibing/tutor-skills.git
cp -r tutor-skills/skills/tutor ~/.claude/skills/
```

> All dependency skills are **recommended but not required**. The core SKILL.md includes inline specs for all formats, so the knowledge base works without any external skills installed.

### Step 2: Install This Skill

```bash
mkdir -p ~/.claude/skills/llm-knowledge
cp llm-knowledge/SKILL.md ~/.claude/skills/llm-knowledge/
```

### Step 3: Create Your Knowledge Base

```bash
# Pick a template that matches your starting point
mkdir ~/my-knowledge-base && cd ~/my-knowledge-base

# Option A: Tech engineer starting point
cp /path/to/templates/tech-engineer/CLAUDE.md ./CLAUDE.md

# Option B: Year 8 student starting point
cp /path/to/templates/year8-student/CLAUDE.md ./CLAUDE.md

# Option C: Start from scratch
cp /path/to/templates/_custom/CLAUDE.md ./CLAUDE.md
```

Templates are just starting points. After copying, open Claude Code and customize:
```
帮我加一个 music 分类到 lifestyle 下面
把 cs/ 从 tech 挪到顶级 domain
```

### Step 4: Set Up Obsidian

In Obsidian: **Open folder as vault** → select your knowledge base directory.

Install Obsidian plugins: **Excalidraw** (Canvas and Bases are built-in since Obsidian 1.8+).

### Step 5: Initialize and Start Using

```
初始化知识库
摄入 https://your-first-url
```

---

## Operations

| Command | Trigger | Description |
|---|---|---|
| **Init** | "初始化知识库" | Create directory structure from domain tree |
| **Ingest** | "摄入 \<URL\>" | Fetch → analyze → write wiki/ + atlas/ |
| **Query** | Ask a question | Search wiki → answer → save to output/ |
| **Lint** | "lint" / "health check" | Check for contradictions, orphans, broken links |
| **Map** | "map \<domain\>" | Regenerate domain canvas from wiki content |
| **Domain** | "add domain \<name\>" / "添加分类 \<name\>" | Add/reorganize domain tree |
| **Exam** | "备考 \<subject\> \<date\>" | Study mode: revision list, flashcards, mock exam |

---

## Supported Data Sources

| Input | Tool |
|---|---|
| Web pages (https://) | web_fetch |
| GitHub repos | GitHub MCP |
| YouTube videos | yt-dlp (subtitles) |
| arXiv papers | web_fetch |
| PDF files | pdftotext / pymupdf |
| Images / handwritten notes | Claude Vision |
| Confluence pages | Confluence MCP |
| Jira issues | Jira MCP |
| GitLab repos | GitLab MCP |

---

## Visual Skills

| Skill | When Triggered | Output |
|---|---|---|
| obsidian-canvas-creator | New L1/L2 domain, Map command, Exam mode | `atlas/<L1>/<L2>/_map.canvas` |
| excalidraw-diagram | Concepts with multi-node relationships | `atlas/<L1>/<L2>/<concept>.excalidraw` |
| mermaid-visualizer | Processes, steps, sequences | Embedded in wiki concept pages |

---

## Compatibility with Other Agents

This skill is designed for Claude Code but the core logic (SKILL.md + CLAUDE.md) works with any LLM agent that can read markdown prompts and write files.

| Layer | Claude Code | Other Agents (OpenClawd, etc.) |
|---|---|---|
| **wiki/** (AI layer) | Full support | Full support — any agent can read/write markdown |
| **atlas/** (human layer) | Full support via @skills | Partial — inline Axton skill logic or skip visualization |
| **Domain management** | Interactive conversation | Agent reads CLAUDE.md config, follows SKILL.md instructions |

**To use with other agents:**
1. Paste SKILL.md content into the agent's system prompt or context
2. Place CLAUDE.md in the knowledge base root (agent reads it as config)
3. For atlas/ generation: either inline the Canvas/Excalidraw format specs, or run wiki/-only mode and generate atlas/ separately in Claude Code

---

## What's Included

```
llm-knowledge-skill/
├── llm-knowledge/
│   └── SKILL.md                    ← Skill engine (shared by all users)
├── templates/
│   ├── tech-engineer/CLAUDE.md     ← Starting point: tech/QA engineer
│   ├── year8-student/CLAUDE.md     ← Starting point: Year 8 student
│   └── _custom/CLAUDE.md           ← Blank template
├── README.md
├── README_CN.md
└── LICENSE
```

---

## Dependencies

### Claude Code Skills

| Skill | Source | Required | Fallback |
|---|---|---|---|
| obsidian-canvas-creator | [axtonliu/axton-obsidian-visual-skills](https://github.com/axtonliu/axton-obsidian-visual-skills) | Recommended | Hand-write Canvas JSON |
| excalidraw-diagram | [axtonliu/axton-obsidian-visual-skills](https://github.com/axtonliu/axton-obsidian-visual-skills) | Recommended | Skip, wiki text only |
| mermaid-visualizer | [axtonliu/axton-obsidian-visual-skills](https://github.com/axtonliu/axton-obsidian-visual-skills) | Recommended | Hand-write mermaid syntax |
| obsidian-markdown | [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills) | Recommended | Follow inline spec in SKILL.md |
| obsidian-bases | [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills) | Optional | No dashboard.base, use index.md |
| tutor | [bevibing/tutor-skills](https://github.com/bevibing/tutor-skills) | Optional | Built-in Exam mode (no interactive tracking) |

### Runtime Tools

| Feature | Tool | Required |
|---|---|---|
| File read/write | Claude Code built-in | Yes |
| Web ingestion | Claude Code built-in web_fetch | Yes |
| Obsidian visualization | Obsidian + Excalidraw plugin | Yes (human layer) |
| GitHub repo ingestion | GitHub MCP | Optional |
| PDF text extraction | pdftotext / pymupdf | Optional |
| YouTube subtitles | yt-dlp | Optional |
| Large-scale semantic search | qmd CLI | Optional (>100 pages) |
| Internal docs | Confluence/GitLab/Jira MCP | Optional |

---

## Credits

- Methodology: Andrej Karpathy, LLM Wiki (2026-04-03)
- Visual Skills: [axtonliu/axton-obsidian-visual-skills](https://github.com/axtonliu/axton-obsidian-visual-skills)
- Obsidian Skills: [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills)
- Tutor Skills: [bevibing/tutor-skills](https://github.com/bevibing/tutor-skills)
