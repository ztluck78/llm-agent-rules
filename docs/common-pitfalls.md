# Common Pitfalls

> Mistakes people make when applying the evidence-first analysis rule.
> 应用证据优先规则时的常见错误。

This document is the **antidote** to the most common failure modes of the rule itself. If you find yourself doing any of the things below, you are not following the rule — you are simulating it.

## Pitfall 1: Reading the Files but Reporting the Patterns

### What it looks like

> "I looked at the codebase. The issue is in `src/utils.ts` — it has a typical `null` check pattern that often breaks in this scenario."

### Why it's wrong

The LLM opened the file but did not cite a specific line. "typical X pattern" is a training-data genre marker, not evidence. The user cannot verify the claim without re-finding the location themselves — which is exactly what the rule is supposed to prevent.

### Correct version

> "I read `src/utils.ts:42`. The function returns `null` when `input.length === 0`, but `caller.ts:17` calls it and dereferences the result without a null check. This is the bug."

### How to detect

Look for phrases like:
- "typically", "usually", "often", "common pattern"
- Section labels without [file:line] references
- Vague references like "the auth module" without a specific file or line

---

## Pitfall 2: Scanning Once, Citing Many

### What it looks like

> "I read 5 files. Here are 12 findings: [file:line] A, [file:line] B, [file:line] C, [file:line] D, ..."

### Why it's wrong

The LLM scanned 5 files but cited 12 locations. Either it cited lines it didn't actually inspect, or it invented locations to pad the response. Both undermine the rule's value: the user now has to verify each citation, and the LLM's signal-to-noise ratio is degraded.

### Correct version

> "I read 5 files. Here are 3 findings (1 severe / 2 minor): ..."

Be honest about the ratio. If you scanned 5 files and found 1 issue, say so. **Incomplete coverage is better than fabricated coverage.**

### How to detect

Count the number of citations in the response. If it exceeds (files_read × ~3), the LLM is likely citing more than it inspected.

---

## Pitfall 3: Conflating "Reading" with "Scanning"

### What it looks like

> "I'll use the Read tool to scan the codebase." (Calls Read once on one file.) "Scan complete."

### Why it's wrong

The rule requires "at least 3 of: Read key files / Run project checks / Search for patterns / Read tool outputs verbatim." One Read call satisfies one of four. A scan that touches one file is not a scan — it's a single read.

### Correct version

> Scan results:
> 1. Read `package.json` (project entry)
> 2. Read `src/index.ts` and `src/utils.ts` (most relevant)
> 3. `git log --oneline -10` (recent commits)
> 4. `grep -rn "TODO" src/` (existing known issues)

### How to detect

Check the opening of the response. A "scan" that lists fewer than 3 distinct tool invocations is a fake scan.

---

## Pitfall 4: Stopping at the First Plausible Cause

### What it looks like

> "Found it! The issue is in `router.ts:23` because the regex doesn't match the new path pattern." (Stops analysis here.)

### Why it's wrong

The LLM found one plausible explanation and stopped. The evidence-first rule requires a **systematic** survey before synthesis, not a single-cause shortcut. The real bug may be elsewhere, or there may be multiple contributing causes.

### Correct version

> I found 3 candidate causes; let me verify each:
>
> 1. `router.ts:23` — regex pattern issue. Verified: matches `oldPattern` but not `newPattern`. ✓
> 2. `middleware.ts:8` — order of operations issue. Verified: `auth` runs before `parse`, but `parse` is what produces the new pattern. ✓
> 3. `config.json:5` — config value mismatch. Verified: still uses old value. ✗
>
> Combined cause: #1 and #2 compound. #3 is unrelated.

### How to detect

Look for "Found it!" or "The issue is" early in the response, followed by a single-cause analysis. A systematic rule should produce **multiple candidates evaluated, then a synthesis**, not a single-cause answer.

---

## Pitfall 5: Synthesizing Causes Without Re-citing Evidence

### What it looks like

> Section 2.1 lists findings with [file:line] evidence.
> Section 2.2 lists causes: "The cause is X." (No [file:line] references in 2.2.)

### Why it's wrong

The output structure requires every claim to be evidence-backed, including synthesized causes. The synthesis step is **not** a license to drop the evidence discipline.

### Correct version

> Section 2.2:
> Cause: race condition between async fetch and state update.
> Evidence: `hook.ts:14` starts an async fetch, but `hook.ts:23` immediately mutates state. The first response of the fetch (at line 28) overwrites the user-set state from line 23.
> Files to change: `hook.ts:14-28` (add proper await + state guard).

### How to detect

For each cause in section 2.2, check whether it references a [file:line] from 2.1. If the synthesis is purely abstract, the rule has been violated.

---

## Pitfall 6: Confident Tone Masking Thin Evidence

### What it looks like

> "I am 100% certain this is a memory leak. The cause is the unclosed subscription. There is no other possibility."

### Why it's wrong

Even with one piece of evidence, **absolute confidence is rarely warranted**. The LLM should distinguish:
- Confirmed by code: "verified by reading lines X-Y"
- Inferred from code: "inferred, but other explanations exist"
- Untested: "not yet verified"

### Correct version

> Strong evidence points to a memory leak:
> - `subscription.ts:42` opens a subscription without a `cleanup` call
> - `consumer.ts:18` calls `subscription.open()` 5 times in a loop, never closing
>
> Confidence: high. Remaining uncertainties: I'm assuming the lifecycle is the React component's, not the surrounding module's. To verify: add a `console.log` in the cleanup function and run the test.

### How to detect

Look for absolute language: "100%", "definitely", "the only cause", "no doubt". Replace with calibrated language: "high confidence", "primary cause", "needs verification".

---

## Pitfall 7: Citing Lines That Don't Exist

### What it looks like

> "[file:line] — bug here" but the file has fewer lines than cited, or line 1424 is empty, or the citation is fabricated.

### Why it's wrong

This is the **death penalty** for the rule. If the LLM cites non-existent lines, the entire output becomes untrustworthy. The user cannot verify anything.

### Correct version

If you are not sure about a line number, say so:
> "I read `auth.ts`. The bug appears to be in the user-validation function, which is around line 30-50 (please confirm by reading the file)."

Do **not** invent a line number to look precise.

### How to detect

Spot-check 2-3 citations against the actual file. If even one is wrong, treat the entire response as suspect.

---

## Pitfall 8: Treating the Output Template as Decoration

### What it looks like

> LLM produces all the required sections (Scan complete, Real findings, Causes, Fixes, Self-check) but the content is generic / pattern-matched / not actually grounded in the scanned files.

### Why it's wrong

The template is a **forcing function**, not a decoration. Producing the template with empty content is the LLM equivalent of filling in a form without reading the application. The user gets structure, but not substance.

### Correct version

If the scan genuinely found nothing, write:
> "Scan complete. Inspected X files / Y commits. No new issues found. Recommend: re-run after the next change."

Do **not** invent findings to fill the template.

### How to detect

If the LLM says "Scan complete" and then lists 5 findings but only ran 1 tool, the template is being treated as decoration.

---

## Pitfall 9: Forgetting the Mandatory Opening

### What it looks like

> "I think the issue is..." (No "Scan complete" opener)

### Why it's wrong

The mandatory opening is the **user's primary signal** that the rule was applied. Without it, the user has to read the entire response to check whether the LLM scanned. This breaks the rule's purpose: making evidence-gathering visible.

### Correct version

Every response must begin with one of:
- "Scan complete. Found N real issues (X severe / Y medium / Z minor): ..."
- "Scan complete. Inspected X files / Y commits. No new issues found."

### How to detect

If the response doesn't open with "Scan complete", the rule has been skipped or applied incorrectly.

---

## Pitfall 10: Applying the Rule to Trivial Fixes

### What it looks like

> User: "Fix the typo on line 12 of `README.md`."
> LLM: "Scan complete. Inspected 3 files. Found 1 issue: `README.md:12` typo. Fix: change `teh` to `the`."

### Why it's wrong

The rule's anti-patterns explicitly say: do not apply to single-line fixes, style conversations, or code completion. For a 1-line typo fix, the rule adds ceremony without value.

### Correct version

> User: "Fix the typo on line 12 of `README.md`."
> LLM: "Done. Changed `teh` to `the` in `README.md:12`."

### How to detect

If the user request is a single concrete action (fix typo, rename variable, add import), do not apply the rule. Reserve it for: "investigate", "analyze", "why does this keep happening", "comprehensive review", "audit".

---

## Summary: The Anti-Checklist

If you are doing any of these, the rule is being violated:

- [ ] Reading 1 file, citing 10 findings
- [ ] "Typical X pattern" without [file:line]
- [ ] Synthesis without re-citing evidence
- [ ] Confident tone without calibrated language
- [ ] Citations that don't exist
- [ ] Template filled with generic content
- [ ] Missing "Scan complete" opener
- [ ] Applying the rule to single-line fixes

The fix is always: **open the file, count the lines, quote them, cite them**. If the rule feels like overhead, you are applying it to the wrong task.

## Related Reading

- [Why Evidence-First?](./why-evidence-first.md) — design rationale
- [Failure Cases](../rules/failure-cases.md) — real failure examples
- [The Core Rule](../rules/evidence-first-analysis.md) — canonical definition
- [Extending the Rules](./extending-rules.md) — how to add new rules that don't have these pitfalls
