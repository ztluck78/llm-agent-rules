# LLM Agent Rules

> A cross-agent ruleset for LLM-driven code analysis.
> **Methodology: scan code first, then reason.**
> 跨 Agent 通用的代码分析规则集：**先证据，后归纳**。

## English

This repository enforces an **evidence-first analysis** methodology for any LLM agent that performs code analysis, debugging, refactoring decisions, or systematic reviews.

**The problem:** LLMs naturally produce "looks-comprehensive" answers via pattern matching from training data. They give 5 well-structured reasons without actually scanning the code. Real bugs that exist in the file get missed.

**The solution:** A rule that forces the LLM to **call tools first, gather real evidence, then analyze**. Every claim must reference a concrete `file:line`.

### 5-Minute Setup

| AI Tool | Integration Path |
|---------|------------------|
| Trae IDE | [integrations/trae/SKILL.md](./integrations/trae/SKILL.md) |
| Cursor | [integrations/cursor/.cursorrules](./integrations/cursor/.cursorrules) |
| Claude Code | [integrations/claude-code/CLAUDE.md](./integrations/claude-code/CLAUDE.md) |
| GitHub Copilot | [integrations/github-copilot/copilot-instructions.md](./integrations/github-copilot/copilot-instructions.md) |
| Aider | [integrations/aider/CONVENTIONS.md](./integrations/aider/CONVENTIONS.md) |
| Continue / Cody / Other | [integrations/continue/.continuerules](./integrations/continue/.continuerules) |

### Core Rule

See [rules/evidence-first-analysis.md](./rules/evidence-first-analysis.md) — the canonical rule definition.

### Why This Matters

See [rules/failure-cases.md](./rules/failure-cases.md) — real failure examples showing what goes wrong when the rule is not applied.

### Quick Start (Copy a Prompt)

See [prompts/full-analysis.md](./prompts/full-analysis.md).

---

## 中文

本仓库强制 LLM Agent 在做代码分析、调试、重构决策、系统复盘时**先扫描代码、再归纳分析**。

**问题：** LLM 倾向通过训练数据的 pattern matching 给出"看起来很全面"的回答——例如 5 条结构化原因。代码里真实存在的 bug 被漏掉。

**解决：** 强制 LLM **先调用工具拿到证据，再做分析**。每条结论必须能引用具体的 `file:line`。

### 5 分钟接入

| AI 工具 | 接入文件 |
|---------|----------|
| Trae IDE | [integrations/trae/SKILL.md](./integrations/trae/SKILL.md) |
| Cursor | [integrations/cursor/.cursorrules](./integrations/cursor/.cursorrules) |
| Claude Code | [integrations/claude-code/CLAUDE.md](./integrations/claude-code/CLAUDE.md) |
| GitHub Copilot | [integrations/github-copilot/copilot-instructions.md](./integrations/github-copilot/copilot-instructions.md) |
| Aider | [integrations/aider/CONVENTIONS.md](./integrations/aider/CONVENTIONS.md) |
| Continue / Cody / 其他 | [integrations/continue/.continuerules](./integrations/continue/.continuerules) |

### 核心规则

见 [rules/evidence-first-analysis.md](./rules/evidence-first-analysis.md)。

### 失败案例库

见 [rules/failure-cases.md](./rules/failure-cases.md) — 真实失败案例，说明不遵循规则会怎样。

### 快速使用（复制 prompt）

见 [prompts/full-analysis.md](./prompts/full-analysis.md)。

---

## Repository Structure

```
llm-agent-rules/
├── README.md                          # This file (bilingual)
├── LICENSE                            # MIT
├── CHANGELOG.md                       # Release history (Keep a Changelog)
├── CONTRIBUTING.md                    # How to propose rules, integrations, fixes
│
├── rules/                             # Canonical rules (markdown, tool-agnostic)
│   ├── evidence-first-analysis.md     # Core rule (6-section template)
│   ├── failure-cases.md               # Real failure examples
│   └── design-rationale.md            # Why the rule is structured this way
│
├── integrations/                      # Per-tool wrapper files
│   ├── trae/SKILL.md                  # Trae IDE
│   ├── cursor/.cursorrules            # Cursor
│   ├── claude-code/CLAUDE.md          # Claude Code
│   ├── github-copilot/copilot-instructions.md  # GitHub Copilot
│   ├── aider/CONVENTIONS.md           # Aider
│   └── continue/.continuerules        # Continue.dev / Cody / others
│   (Each integration directory also has its own README.md with install steps)
│
├── prompts/                           # Copy-paste prompts
│   └── full-analysis.md               # English + Chinese
│
├── examples/                          # Real usage examples
│   └── good-bad-comparison.md         # Side-by-side
│
├── docs/                              # Deep-dive documentation
│   ├── why-evidence-first.md          # Design rationale (deeper)
│   ├── extending-rules.md             # How to add new rules
│   └── common-pitfalls.md             # 10 mistakes when applying the rule
│
└── .github/
    ├── workflows/validate.yml         # CI: validates rule structure
    ├── PULL_REQUEST_TEMPLATE.md       # PR template with quality bar
    └── ISSUE_TEMPLATE/
        ├── bug_report.yml
        ├── feature_request.yml
        └── question.yml
```

## License

MIT — see [LICENSE](./LICENSE).
