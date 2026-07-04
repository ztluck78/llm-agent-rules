# Contributing to llm-agent-rules

Thank you for your interest in improving this ruleset. This document explains how to add new rules, propose changes, and submit quality contributions.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [What to contribute](#what-to-contribute)
- [How to propose a new rule](#how-to-propose-a-new-rule)
- [How to fix or improve an existing rule](#how-to-fix-or-improve-an-existing-rule)
- [Quality bar](#quality-bar)
- [Review process](#review-process)

## Code of Conduct

This project follows a simple principle: **be precise, be kind, be evidence-based.** When discussing rule design, cite specific examples (good or bad) rather than abstract preferences.

## What to contribute

We welcome contributions in these categories:

1. **New rules** — additional methodology rules that follow the same evidence-first pattern
2. **New integrations** — format wrappers for AI tools not yet supported
3. **Failure cases** — real examples where the rule would have helped (added to `rules/failure-cases.md`)
4. **Translations** — accurate translations into other languages
5. **Documentation** — clarifications, examples, edge cases
6. **Bug fixes** — typos, broken links, format issues

We are **less** interested in:
- Generic "best practices" content (the rule explicitly blocks this)
- Hypothetical failure cases (must be real observed cases)
- Tool integrations that don't yet exist or are unmaintained

## How to propose a new rule

A new rule must include:

1. **A clear trigger** — when should the rule be applied? (keywords, question patterns)
2. **A clear scope** — when should it NOT be applied?
3. **The 6-section structure** (see `rules/evidence-first-analysis.md` as a template):
   - When to apply
   - Core discipline
   - Mandatory procedure
   - Output structure
   - Anti-patterns
   - Self-check
4. **At least one real failure case** — what bad output does the rule block?
5. **At least one good example** — what does correct output look like?
6. **Design rationale** — why this rule, why this structure (in 1-2 paragraphs)

Use `docs/extending-rules.md` (when available) as a detailed guide.

### PR template for new rules

```markdown
## Rule: <name>

### Trigger
- Keywords: ...
- When applied: ...
- When NOT applied: ...

### Real failure case (without this rule)
<paste actual bad output, with file:line references>

### Real good case (with this rule)
<paste actual good output>

### Design rationale
<why this rule exists, in 1-2 paragraphs>

### Checklist
- [ ] 6-section structure present
- [ ] At least 1 real failure case documented
- [ ] At least 1 good example documented
- [ ] Design rationale included
- [ ] CHANGELOG.md updated
- [ ] At least 1 tool integration file added (if applicable)
```

## How to fix or improve an existing rule

1. Open an issue first describing the problem (cite a specific section / line).
2. In the PR, explain: what change, why, and what evidence.
3. If your change adds a new failure case, include the actual bad output.

Small fixes (typos, broken links, formatting) can be PR'd directly without an issue.

## Quality bar

Every contribution is evaluated against three criteria:

### 1. Evidence-based
- Every claim must be backed by a real example (not hypothetical).
- If you say "this pattern fails because X", show the actual failure.

### 2. Concrete and actionable
- A reader should be able to follow the rule without further interpretation.
- Avoid "consider" / "be careful" / "it's good practice" — use "do X" / "do not do Y" / "edit file:line".

### 3. Self-consistent
- The rule should not contradict other rules in the repository.
- The rule should follow the 6-section structure unless there's a strong reason not to.

## Review process

1. **Automated checks** (when configured) run on every PR:
   - File format validation
   - Link checking
   - Required sections present

2. **Maintainer review** by @ztluck78 or designated reviewers:
   - Quality bar check
   - Consistency with existing rules
   - Failure case verification

3. **Iteration**: 1-3 review cycles are normal. Don't be discouraged by review feedback — it's how rules get sharper.

4. **Merge**: Once approved, the maintainer will merge and update CHANGELOG.md.

## Style guide

- **Language**: English-first for canonical documents; Chinese translations welcome as separate files (`*.zh-CN.md`).
- **Tone**: Direct and instructional. Avoid hedging language ("perhaps", "you might want to").
- **Examples**: Use real code snippets with real file paths. Generic `<example>` placeholders are discouraged.
- **Length**: Each rule should be ≤ 500 lines. If longer, split into multiple rules.

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
