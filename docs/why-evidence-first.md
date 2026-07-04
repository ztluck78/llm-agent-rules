# Why Evidence-First?

> Deep dive into the problem this rule solves and the design choices behind it.
> 深入讨论：这个规则要解决的问题，以及背后的设计选择。

## The Problem

Modern LLM assistants are powerful but have a recurring failure mode when given open-ended analysis questions:

**The LLM produces "looks-comprehensive" answers based on pattern matching, not on actual investigation of the user's code.**

This failure is hard to spot because:
- The answer is well-structured (numbered lists, clear categories)
- The language is confident ("There are 5 reasons...")
- The structure mirrors what an expert might say
- But none of it is grounded in the actual codebase

The user receives a confident answer, trusts it, and walks away — **without realizing the real bug was sitting in the file, unmentioned**.

## Why LLMs Default to This Mode

LLMs are trained on huge corpora of "analysis-like" text:
- Stack Overflow answers
- Technical blog posts
- Code review comments
- Engineering decision records

When asked to analyze, the LLM **imitates this genre** — structured, reasoned, complete. But the source of the structure is genre imitation, not the actual codebase.

Three reinforcing factors:

1. **Training reward**: well-structured, confident text gets higher ratings
2. **No penalty for fabrication**: text that sounds right isn't penalized for being wrong
3. **No mechanism for grounding**: the LLM has no built-in requirement to cite evidence

The result: a default response mode that's structurally excellent but empirically empty.

## The Cost

This failure mode is **expensive in subtle ways**:

| Cost | Detail |
|------|--------|
| **Time waste** | User reads the answer, finds it "plausible", doesn't realize the real bug was missed |
| **False confidence** | The LLM's confident tone masks the absence of evidence |
| **Reinforcement** | User accepts the answer, comes back next time, expects the same |
| **Skill atrophy** | User stops doing their own verification |

The most insidious cost is the **last one**. When a developer learns to trust LLM analysis without verification, they lose the habit of evidence-based reasoning. The LLM doesn't just give wrong answers — it changes how the user thinks.

## The Fix: Structural, Not Stylistic

A naive fix is to add a line to the LLM's prompt: "please be accurate and cite sources." This doesn't work. Style instructions are easy to forget, especially under long contexts.

**The fix is structural.** The rule must:

1. **Make evidence-gathering visible** to the user (mandatory opening with file:line)
2. **Make failure visible** (anti-patterns, failure-cases file)
3. **Make deviation visible** (self-check, explicit "do not apply" list)

If the LLM produces an analysis without these signals, the user can immediately call it out. If the LLM tries to skip a section, the structure forces it to either justify the skip or fail visibly.

This is a **forcing function** — the rule works not because the LLM is good, but because the rule makes bad behavior **easy to detect**.

## What "Evidence" Means

In this rule, evidence means:

- A specific file path and line number
- A verbatim quote of grep / test output / error message
- A commit hash or git log entry
- A measurement (e.g. "third skill tag overflows by 87px on 375px viewport")

What is **not** evidence:

- "Generally, when X happens, Y is the cause" (no specific case)
- "This is a common pattern" (no specific instance)
- "Best practice suggests" (no grounded claim)
- "It's likely that..." (probability language that hides lack of certainty)

## Why "Scan Before Analyze" Specifically

The order matters. If the LLM analyzes first and then looks for evidence to support its analysis, it will **find evidence for its priors** (confirmation bias). If it scans first and then analyzes, the analysis is grounded in what actually exists.

This is the same principle as scientific method: observations should come before theory, not the other way around.

In code terms:
- "Scan first" = open files, run checks, look at history
- "Analyze second" = synthesize findings into causes
- "Recommend third" = propose specific fixes

If any of these steps is missing, the chain breaks.

## What This Rule Is Not

It's worth stating what this rule does **not** try to do:

- **It is not a coding style guide.** The rule does not say "use TypeScript" or "write tests". Those are general best practices; this rule is about evidence.
- **It is not a replacement for human judgment.** Even with the rule, a human must review the LLM's output. The rule makes the review easier, not unnecessary.
- **It is not a guarantee of correctness.** A finding can be wrong even with `[file:line]` evidence. The rule improves verifiability, not infallibility.
- **It is not one-size-fits-all.** For trivial fixes, style questions, and pure code generation, the rule adds overhead. Apply selectively.

## How to Know If The Rule Is Working

Track these signals over time:

1. **Fewer "looks comprehensive but misses the bug" complaints** from users
2. **More pushback from users** when the LLM produces generic output (because the rule makes the structure visible)
3. **Shorter LLM responses** on average (because synthesis replaces improvisation)
4. **More actionable commits** (because fixes are specific, not vague)

If the rule is producing the opposite signals (longer, more verbose, more generic), the rule is being applied as a **checklist ritual** rather than a forcing function. In that case, revisit the design — likely some constraint is being satisfied formally but not substantively.

## Related Reading

- [Design Rationale](../rules/design-rationale.md) — why the rule is structured this way
- [Failure Cases](../rules/failure-cases.md) — real examples of what goes wrong
- [The Core Rule](../rules/evidence-first-analysis.md) — the canonical rule definition
