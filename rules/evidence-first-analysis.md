# Evidence-First Analysis Rule

> The canonical rule. Cross-agent, language-agnostic, tool-agnostic.
> 核心规则。跨 Agent、跨语言、跨工具。

## When to Apply

Apply this rule when the user asks for any of the following:

- "全面分析 / 系统排查 / 复盘 / 调研 / 全面了解 / audit / investigation / review / assess / deep dive"
- "为什么反复出这个问题 / 这个 bug 怎么产生的"
- Any open-ended question requiring **systematic understanding** of code or a problem

**Do NOT apply** to: simple Q&A, single-line fixes, code completion, style conversations.

## Core Discipline

**Call tools to scan the code → gather real evidence → then analyze and synthesize.**
**Forbidden:** giving abstract insights before evidence, using pattern matching as a substitute for grep.

## Step 1: Mandatory Scanning Actions

Before giving any analysis, **invoke tools** to collect real evidence. Complete at least 3 of the following 4 categories:

### 1.1 Read Key Files
- Project entry points (`package.json`, `README`, `AGENTS.md`, `CONTRIBUTING.md`)
- 3–5 source files most relevant to the question
- Recent commit history (e.g. `git log --oneline -10`)

### 1.2 Run Project Checks (if available)
- Data consistency / schema validation
- Linter / formatter
- Type checker
- Unit tests

### 1.3 Search for Patterns
- Use grep / search for keywords related to the question
- Use file listing to understand the relevant directory structure
- Specifically search for **bugs that already exist but no one has mentioned yet**

### 1.4 Read Tool Outputs Verbatim
- Quote actual grep results, test output, error messages
- Do not paraphrase tool outputs into vague summaries

## Step 2: Mandatory Output Structure

### 2.1 Real Findings (Required)

```
- [file:line] Phenomenon: <what is wrong>
  Evidence: <grep result / test output / commit info>
  Severity: High / Medium / Low
```

### 2.2 Systematic Causes (Synthesized from 2.1)

**Do not** synthesize causes from training-data experience.
**Do** synthesize causes from the actual findings in 2.1.

Each cause **must** reference at least one finding from 2.1 as evidence.

### 2.3 Fix Recommendations (Specific to Files)

List which files, which lines, and what change to make.
**Do not** write "in the future, pay attention to X / Y / Z".
**Do** write "now change `file:line` to add `min-width: 0`".

## Anti-Patterns (Explicitly Forbidden)

- ❌ "Give 5 reasons first, then add evidence as an afterthought"
- ❌ Abstract "systemic problems" with no concrete `file:line` reference
- ❌ Skipping the scan and saying "I think..."
- ❌ Pattern matching substituting for grep ("this kind of problem is usually caused by X")
- ❌ Listing "best practices" from outside the code without referencing the actual code
- ❌ Confident presentation masking the absence of evidence

## Mandatory Opening

Every response must begin with:

```
Scan complete. Found N real issues (X severe / Y medium / Z minor):
1. [file:line] Phenomenon / Evidence
2. [file:line] Phenomenon / Evidence
...
```

**If the scan finds no issues**, still write explicitly:
"Scan complete. Inspected X files / Y commits. No new issues found."
**Do not** silently default to "no problems".

## Self-Check (Before Sending)

- [ ] I invoked at least 3 tools to gather evidence
- [ ] Every claim references a concrete `file:line`
- [ ] I did not write "this kind of problem is usually caused by X, Y, Z" (that is deduction, not induction)
- [ ] Fix recommendations are specific to files, not "be careful in the future"
- [ ] The response begins with a "Scan complete" signal

## Failure Mode Reference

The "5 well-structured causes" answer style is the canonical failure mode this rule exists to block:

- **Surface:** Looks comprehensive, well-organized
- **Reality:** Deduction from training-data pattern matching
- **Damage:** Misses the real bugs that actually exist in the code

This rule is designed to block that failure mode at the structural level.
