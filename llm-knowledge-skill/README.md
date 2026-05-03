# LLM Knowledge Skill

A personal knowledge base toolkit powered by Claude Code. Based on the Karpathy LLM Wiki methodology, extended with a multi-layer architecture and visual skills integration.

**Core philosophy: your knowledge base follows you, not your role or project.** It starts from a template matching your current stage, then grows with your interests and career over time.

---

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

## Quick Start — Claude Code

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

Install the required plugins (see the [Obsidian Plugins](#obsidian-plugins) section below for full details). TL;DR: install the **Excalidraw** community plugin — without it, `.excalidraw` files display as raw JSON.

### Step 5: Initialize and Start Using

> **Important — start a new session first.**
> After copying CLAUDE.md, the `@llm-knowledge` skill is not yet loaded in your current session. Close Claude Code and reopen it in the knowledge base directory so the skill is auto-loaded from CLAUDE.md.
>
> Do **not** invoke the skill via `/llm-knowledge` or the Skill tool — it is a local skill, not a marketplace plugin.

```
初始化知识库
摄入 https://your-first-url
```

---

## Quick Start — OpenClawd / Other Agents

For agents that don't support Claude Code's `~/.claude/skills/` directory (OpenClawd, custom LLM agents, etc.):

### Step 1: Set Up the Knowledge Base

```bash
mkdir ~/my-knowledge-base && cd ~/my-knowledge-base

# Copy a template as your config
cp /path/to/templates/tech-engineer/CLAUDE.md ./CLAUDE.md
```

### Step 2: Load SKILL.md into the Agent

**Option A — System prompt (recommended):**
Copy the full content of `llm-knowledge/SKILL.md` into the agent's system prompt or context window.

**Option B — File-based loading:**
If the agent can read local files, place SKILL.md in the knowledge base root:
```bash
cp llm-knowledge/SKILL.md ~/my-knowledge-base/SKILL.md
```
Then instruct the agent to read it on startup.

### Step 3: Dependency Skills Handling

External `@skill` calls (e.g. `@obsidian-canvas-creator`) won't work outside Claude Code. SKILL.md includes inline fallback specs, so:

| Feature | What happens |
|---|---|
| wiki/ (concept pages, index, log) | Works fully — plain markdown read/write |
| Mermaid diagrams | Works — agent writes mermaid code blocks directly |
| Canvas (_map.canvas) | Agent writes Canvas JSON directly using spec in SKILL.md |
| Excalidraw diagrams | Skipped — generate wiki text only, add diagrams later in Claude Code |
| Obsidian Bases (dashboard.base) | Skipped — use wiki/index.md for navigation |

### Step 4: Set Up Obsidian

Same as Claude Code: open the knowledge base folder as an Obsidian vault and install the plugins described in the [Obsidian Plugins](#obsidian-plugins) section.

### Step 5: Initialize and Start Using

Tell the agent: `初始化知识库` or `Initialize the knowledge base`

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

**stream/ is optional.** If your CLAUDE.md does not declare a Stream Configuration section, the skill skips stream creation and Lint stream checks. Add stream subtypes to your CLAUDE.md if you have external automation pushing periodic content (cron jobs, scheduled agents, daily/weekly digests).

The `Init` operation creates the layout based on CLAUDE.md. The `Lint` operation validates that the actual layout matches what CLAUDE.md declares.

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

## Obsidian Plugins

The `atlas/` layer produces files that Obsidian renders through plugins. Without them, some files display as raw JSON or plain text.

### Enable Community Plugins First

Community plugins are disabled by default (Obsidian's **Restricted Mode / 安全模式**). You need to turn it off before any community plugin can be installed:

1. Obsidian → **Settings (⚙️)** → **Community plugins**
2. Click **Turn on community plugins** (or **Turn off Restricted Mode** on newer versions)
3. A security warning appears — confirm to proceed
4. The **Browse** button becomes active → you can now reach the community plugin marketplace

### Required

**Excalidraw** (community plugin by Zsolt Viczián)

Required to render `.excalidraw` files (concept relationship diagrams, visual periodic tables, etc.). Without it, `.excalidraw` files show as raw JSON.

Install:
1. **Community plugins** → **Browse** → search `Excalidraw` → **Install** → **Enable**
2. (Optional, recommended) Command palette → `Excalidraw: Convert *.excalidraw files to *.excalidraw.md` — wraps files in markdown containers so wikilinks, frontmatter, and search work.

Fallback if you can't install it: open [excalidraw.com](https://excalidraw.com), use **Open** to load the `.excalidraw` file. Read-only browsing only — no Obsidian cross-linking.

### Built-in (no install needed)

| Plugin | Status | What it renders |
|---|---|---|
| **Canvas** | Core plugin (Obsidian 1.1+), enabled by default | `.canvas` files — `_map.canvas` domain maps, `overview.canvas` |
| **Bases** | Core plugin (Obsidian 1.9+, beta) | `.base` files — `dashboard.base` data views. If on an older Obsidian or if Bases is disabled: Settings → Core plugins → enable **Bases** |
| **Mermaid** | Native markdown support | ```` ```mermaid ```` code blocks embedded in wiki concept pages |
| **MathJax / LaTeX** | Native markdown support | `$…$` inline and `$$…$$` block math in concept pages |

### Rendering Enhancers (recommended — install via Browse)

These improve the visual quality of the `atlas/` and `wiki/` layers beyond default Obsidian rendering:

| Plugin | Why install |
|---|---|
| **Style Settings** | Required dependency for most good themes (Minimal, Things, Blue Topaz). Exposes granular CSS variables (font size, spacing, callout colours) for customisation |
| **Iconize** (previously *Icon Folder*) | Adds icons to files/folders — makes domain trees much easier to scan visually (e.g. 📘 on `wiki/concepts/academic/`, 🧪 on `science/`) |
| **Advanced Tables** | Auto-formats and navigates large markdown tables. The `vocab/` and `expressions/` pages rely on tables — editing them without this plugin is painful |
| **Image Toolkit** | Click-to-zoom for images and mermaid/excalidraw previews. Useful when atlas diagrams get dense |
| **Highlightr** | Multi-colour highlights (beyond the default `==yellow==`). Useful when colour-coding vocab by confidence or concept type |

### Productivity Plugins (optional)

Not strictly about rendering, but fit this knowledge base's workflow:

| Plugin | Why |
|---|---|
| **Dataview** | Query wiki frontmatter (tags, domain, updated) in inline tables. Alternative to Bases on older Obsidian versions |
| **Templater** | Template engine — useful if you ever create concept pages manually instead of via Claude |
| **Tag Wrangler** | Rename, merge, or browse tags across the whole vault (the KB accumulates many tags over time) |
| **Outliner** | Smoother editing of the domain tree bullet list in CLAUDE.md |
| **Linter** | Auto-format markdown on save (trailing whitespace, heading spacing, YAML frontmatter) |

### Theme

The default Obsidian theme works fine, but for noticeably better readability on long concept pages:

- **Minimal** (by kepano) — clean, typography-focused. Pair with **Style Settings** for fine-tuning
- **Things** — softer colours, good for atlas browsing

Apply via **Settings → Appearance → Themes → Manage → Browse**.

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
| Obsidian visualization | Obsidian + Excalidraw plugin (see [Obsidian Plugins](#obsidian-plugins)) | Yes (human layer) |
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
