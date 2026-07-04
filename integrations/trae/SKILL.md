---
name: evidence-first-analysis
description: "Evidence-first code-grounded analysis for Trae IDE. Triggers when user asks for 全面分析/系统排查/复盘/调研/全面了解/audit/investigation/review. MUST scan code with tools first, then analyze based on real data. Do not give abstract insights without concrete file:line evidence."
---

# Evidence-First Analysis (Trae IDE)

> A Trae-native version of the cross-agent evidence-first rule.
> 跨 Agent 规则在 Trae IDE 的本地化版本。

This Skill enforces the same methodology as [`rules/evidence-first-analysis.md`](../../rules/evidence-first-analysis.md), adapted to Trae IDE's tool surface.

## When Trae Invokes This Skill

The Skill auto-triggers when the user message contains any of:
- 全面分析 / 系统排查 / 复盘 / 调研 / 全面了解
- audit / investigation / review / assess / deep dive
- 为什么反复出这个问题 / 这个 bug 怎么产生的

## Mandatory Procedure

### Step 1: Tool-Based Scan (Required Before Any Analysis)

Use Trae tools to gather real evidence. Complete **at least 3** of the following:

| Category | Trae Tool | Action |
|----------|-----------|--------|
| Read key files | `Read` | Read project entry, 3-5 source files, recent commits |
| Run project checks | `RunCommand` | `npm run check:data`, `npm run lint`, `npm test`, etc. |
| Search patterns | `Grep` | Search for keywords related to the question |
| List file structure | `Glob` | List relevant directories |
| Git history | `RunCommand` | `git log --oneline -10`, `git log -p <file>` |

### Step 2: Mandatory Output Structure

Every response must follow this structure:

```
Scan complete. Found N real issues (X severe / Y medium / Z minor):

1. [file:line] Phenomenon: <what is wrong>
   Evidence: <grep result / test output / commit info>
   Severity: High / Medium / Low

2. ...

Systematic causes (synthesized from the findings above):
1. <cause 1> — supported by finding #X
2. <cause 2> — supported by finding #Y

Fix recommendations (specific):
- Edit file:line: <change>
- ...
```

### Step 3: Anti-Patterns (Explicitly Forbidden)

- ❌ "Give 5 reasons first, then add evidence as an afterthought"
- ❌ Abstract "systemic problems" with no concrete `file:line` reference
- ❌ Pattern matching substituting for grep
- ❌ Confident presentation masking the absence of evidence

## Self-Check (Before Sending)

- [ ] I invoked at least 3 Trae tools (Read / Grep / RunCommand / Glob) to gather evidence
- [ ] Every claim references a concrete `file:line`
- [ ] I did not write "this kind of problem is usually caused by X, Y, Z" (that is deduction, not induction)
- [ ] Fix recommendations are specific to files
- [ ] The response begins with a "Scan complete" signal

## Installation

This Skill is already installed under `~/.trae-cn/builtin/global/skills/evidence-first-analysis/`. To update, re-copy this file.

## License

MIT — see [LICENSE](../../LICENSE).
