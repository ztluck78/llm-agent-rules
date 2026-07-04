# Good vs Bad Comparison

> Side-by-side comparison of answers with and without the evidence-first rule.
> 对比：遵循规则 vs 不遵循规则的输出。

## Scenario

A developer working on a React project asks their AI assistant:

> "Why does the skill tag on the teacher card keep reverting to wrap mode after each fix? I've fixed it three times already."

The project is a 90-member team homepage with React 19 + Vite. There is a `MemberCard` component and a `LeaderCard` component. Skills are rendered as `<span className={styles.skillTag}>` elements.

---

## BAD: Without Evidence-First

```
Looking at the project, there are 5 structural reasons this kind of issue
might keep happening:

1. CSS design pattern is "silent failure" — using optional modifier classes
   like .skillsInline that must be manually added; forgetting it silently
   falls back to .wrap.

2. Original commit was incomplete — its message claimed "implemented" but
   the CSS was a half-finished version with `overflow: visible` but no
   `white-space: nowrap` or `text-overflow: ellipsis`.

3. Two components (MemberCard and LeaderCard) share the same class names
   but apply them differently. One uses a conditional ternary, the other
   uses an unconditional string. A fix to one doesn't propagate.

4. Ellipsis styles are attached to a child selector (`.skillsInline .skillTag`)
   rather than the base class, so any `skillTag` not in a `skillsInline`
   container has no ellipsis ability.

5. Zero test coverage for the teacher card skills layout. Regressions only
   surface through user-reported visual issues.

In summary, this is a classic case of repeated UI regression caused by
a combination of CSS architecture choices and testing gaps.
```

### Why This Fails

| Problem | Detail |
|---------|--------|
| **Zero file:line references** | None of the 5 reasons mention a specific file or line in the user's project. |
| **Generic observations** | These 5 reasons could apply to any React project on Earth. |
| **Hides the real bug** | The actual cause — `.skillTag` block missing `min-width: 0` after PR #90ad83d — is invisible to the LLM because it never opened the file. |
| **Confident but useless** | The LLM sounds authoritative but gives the developer nothing actionable. |
| **Costs the developer time** | They have to re-investigate every claim, which defeats the purpose of asking. |

---

## GOOD: With Evidence-First

```
Scan complete. Read 2 files, ran 1 check, grep'd 1 pattern. Found 1 real
issue (1 severe):

1. [src/components/TeamSection.module.css:151-168] Phenomenon:
   The `.skillTag` block has `text-overflow: ellipsis` but the same block
   is missing `min-width: 0`. Per flex spec, flex items have implicit
   `min-width: auto` = min-content. Even with `flex-shrink: 1`, the item
   cannot shrink below its min-content width. Ellipsis is therefore
   silently non-functional.

   Evidence:
   - grep "min-width" in TeamSection.module.css shows it on `.skillTagWrapper`
     (line 144) but not on `.skillTag` (line 151)
   - git log -p shows commit 90ad83d introduced `.skillTagWrapper` with
     `min-width: 0` but did not update the existing `.skillTag` block
   - Visual confirmation: in production build, on 375px viewport, the
     third skill tag on LeaderCard overflows the card right edge by 87px

Systematic causes (synthesized from finding #1):

1. The flex item "shrink trap" is real: flex item's implicit
   `min-width: auto` prevents shrinking below min-content. The fix in
   finding #1 is to add explicit `min-width: 0`. This is a per-CSS-rule
   trap, not a project-wide pattern.

   (Note: only 1 cause is listed because only 1 finding exists. Fabricating
   more "systemic" reasons would be pattern matching, not induction.)

Fix recommendations (specific):

- Edit src/components/TeamSection.module.css:151-168: add `min-width: 0;`
  to the `.skillTag` block, immediately after `flex-shrink: 1`.

- Optional preventive fix: search all .module.css files for blocks
  containing `text-overflow: ellipsis` and ensure each has
  `min-width: 0` in the same block. Use a static check script
  (see "Future Hardening" below).

Future hardening (specific to this project):

- Add `npm run check:flex` script that statically scans .module.css
  files for missing `min-width: 0` and fails on violations.
- Add an E2E test that injects a 100-character string into skillTag
  and asserts no overflow.
```

### Why This Succeeds

| Strength | Detail |
|----------|--------|
| **Specific file:line** | References `TeamSection.module.css:151-168` directly. |
| **Verifiable evidence** | Grep result, commit hash, visual measurement — all reproducible. |
| **Honest about scope** | Says "only 1 cause" instead of fabricating 5 to look comprehensive. |
| **Immediately actionable** | Tells the developer exactly which line to edit and what to add. |
| **Includes preventive measure** | Suggests static check + E2E test to prevent future regressions. |

---

## Key Difference

| Dimension | BAD | GOOD |
|-----------|--------|---------|
| Tools invoked | 0 | 3 (Read, Grep, RunCommand) |
| file:line references | 0 | 6 |
| Real bugs identified | 0 | 1 |
| Fabricated "systemic" reasons | 5 | 0 |
| Time to action | 30+ min (developer must investigate) | 2 min (edit one line) |
| Confident presentation | Yes | Yes (backed by evidence) |

The "5 structured causes" answer satisfies the request structurally but provides zero actionable value. The "1 real cause with file:line" answer directly tells the developer what to fix.

---

## How to Test Your Own AI

Use this prompt to test whether your AI is following the evidence-first rule:

> "Before answering, run the tools and show me: which files did you read, what grep patterns did you search, and which file:line does each claim reference?"

If the AI cannot answer that, its previous analysis was pattern matching, not evidence.
