# Design Rationale

> Why this rule is structured the way it is.
> 为什么这样设计这个规则。

This document explains the **structural reasoning** behind every choice in `evidence-first-analysis.md`. The goal is to make the rule's design self-justifying — anyone reading the rule should understand **why** each constraint exists, not just **what** it says.

## 1. Why "Evidence First"?

### The Failure Mode This Solves

LLM assistants, when given an open-ended analysis question, default to a **pattern-matched response**:

```
User: "Why does X keep happening?"

LLM (default): "Here are 5 structural reasons X might keep happening:
1. ...
2. ...
3. ...
"
```

This response **looks comprehensive**. It uses parallelism, numbered lists, and confident tone. But the content is **deduced from training-data patterns** — the LLM has not actually read the user's code, run any check, or verified any claim.

The damage: the developer reads the answer, finds it "plausible," and **doesn't know that the actual bug is sitting in the file, unmentioned**.

### Why This Mode Is the Default

LLMs are trained on a massive amount of "analysis-like" text (Stack Overflow answers, blog posts, technical explanations). When asked to analyze, they **imitate that genre** — structured, reasoned, complete. But the source of the structure is genre imitation, not the actual codebase.

**The fix:** force the LLM to **ground** its response in tool outputs from the real codebase. A claim without `file:line` is a claim without ground.

## 2. Why a 6-Section Rule?

Each section blocks a specific failure mode. The order matters.

| Section | Failure Mode It Blocks | Mechanism |
|---------|------------------------|-----------|
| **When to apply** | Over-application: triggering on simple Q&A wastes time and produces verbose answers for trivial questions | Explicit exclusion list |
| **Core discipline** | Free-form answer: LLM uses its own structure | Single sentence: "scan → evidence → analyze" |
| **Step 1: scanning** | Skipping the scan entirely | "Mandatory" + "at least 3 of 4 categories" |
| **Step 2.1: real findings** | Fabricating "systemic" causes without evidence | Required `[file:line]` format |
| **Step 2.2: causes** | Induction from training data | "Must reference finding #N" |
| **Step 2.3: fixes** | Vague recommendations | "Specific to file:line, not 'be careful'" |

**Why 6 and not 4 or 10?** Fewer sections (e.g. only "scan first" + "cite evidence") leaves too much room for interpretation. More sections (e.g. splitting each step into 3 sub-rules) becomes unreadable. 6 is the sweet spot.

## 3. Why "Mandatory Opening"?

```
Scan complete. Found N real issues (X severe / Y medium / Z minor):
1. [file:line] Phenomenon / Evidence
2. ...
```

The opening has a specific purpose: **make evidence-gathering visible to the user**.

Without this:
- LLM could write a long analysis, then bury the scan results at the end
- User has no way to verify whether the LLM actually scanned

With this:
- The very first thing the user sees is whether the LLM did the work
- If "Found 0 issues" appears after a scan, the user knows the conclusion is real
- If the LLM tries to skip the scan, the user can immediately call it out

This is a **structural verification mechanism** — the user can fail-fast on rule violations.

## 4. Why List Anti-Patterns Explicitly?

Anti-patterns are stated as **❌** examples of what NOT to do. Why not just describe the correct pattern?

Because LLMs (and humans) learn faster from negative examples. The phrase "Give 5 reasons first, then add evidence as an afterthought" is the **specific failure pattern** observed in the wild. Naming it explicitly makes it recognizable.

This is the same principle as [failure-cases.md](./failure-cases.md) — concrete failure examples have higher impact than abstract principles.

## 5. Why the Self-Check?

The self-check at the end is a **forcing function**. The LLM has to evaluate itself before sending:

```
- [ ] I invoked at least 3 tools
- [ ] Every claim references file:line
- [ ] ...
```

Two purposes:
1. **Catches honest mistakes**: the LLM might have used only 1 tool but think it used 3. The checklist forces a literal count.
2. **Communicates to the user**: the user sees the LLM's self-evaluation, knows which boxes are checked, can challenge unchecked ones.

## 6. Why No "Severity Scoring" Guidance?

Some rules use a 1-5 severity scale. This rule uses High/Medium/Low.

Reason: **severity scoring requires context** (impact, frequency, user-facing-ness) that the LLM cannot reliably determine without deeper investigation. Forcing a 1-5 score would either:
- Be arbitrary (LLM invents a number)
- Trigger more analysis (defeats the "evidence first" speedup)

High/Medium/Low is intentionally coarse — it's enough to communicate priority without inviting fabricated precision.

## 7. Why No "Best Practices" Section?

Some analysis rules include a "general best practices" section (e.g. "always write tests", "use TypeScript"). This rule explicitly **does not** include one.

Reason: best practices disconnected from the actual code are exactly what the rule exists to block. Including a generic best-practices section would undermine the discipline.

If a finding from the actual code leads to a general principle, the principle **must** be stated in the context of the finding, not as standalone advice.

## 8. Why the Failure-Cases.md File Is Separate?

`failure-cases.md` is structurally separate because:
- **Rules change slowly**, but **failure cases accumulate** as more bad examples are observed
- Reading the rule + reading examples together is more impactful than reading a long rule with examples inline
- New failure cases can be added without changing the rule

## 9. How This Rule Is Meant to Be Used

| Usage Mode | Mechanism |
|------------|-----------|
| **Project-level rules** | Drop the file into `.cursorrules`, `CLAUDE.md`, etc. |
| **Global rules** | Trae global Skills, Cursor user-level rules |
| **Per-prompt** | Copy a prompt from `prompts/` and paste |
| **External reference** | Reference the URL in a system prompt |

The same content adapts to all four modes. The tool-specific files in `integrations/` are **format wrappers**, not different rules.

## 10. When to Break the Rule

This rule is a default, not a law. Cases where deviation is acceptable:

1. **Trivial fixes**: "rename this variable" — the user knows what they want, evidence is unnecessary
2. **Pure code generation**: "write a function that does X" — no analysis needed
3. **Style conversations**: "is this code idiomatic?" — subjective, no falsifiable claim
4. **Meta-questions about the rule itself**: "what does evidence-first mean?" — answering this doesn't require scanning code

For all other cases, follow the rule. The 6 sections are designed to make deviation visible — if the LLM wants to skip a section, it should justify why.

## Summary

The rule's design follows a single principle: **make evidence visible, make failure visible, and make deviation visible**.

- **Evidence visible**: mandatory opening with `[file:line]` references
- **Failure visible**: anti-patterns and failure-cases file
- **Deviation visible**: explicit "do not apply" list and self-check

If a future revision violates this principle (e.g. by adding vague "best practices" sections), it should be rejected.
