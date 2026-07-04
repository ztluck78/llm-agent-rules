# Failure Cases

> Real examples of what goes wrong when the evidence-first rule is NOT applied.
> 真实失败案例：不遵循规则会得到什么样的错误输出。

## Case 1: "5 Structured Causes" — The Most Common Failure

### Context

A developer asks: "Why does the skill tag on the teacher card keep reverting to wrap mode after each fix?"

### The Wrong Answer (Without Evidence-First)

```
Looking at the project, there are 5 structural reasons this might keep happening:

1. CSS design pattern is "silent failure" — using optional modifier classes
   like .skillsInline that must be manually added; forgetting it silently
   falls back to .wrap.

2. Original commit was incomplete — its message claimed "implemented" but
   the CSS was a half-finished version with `overflow: visible` but no
   `white-space: nowrap` or `text-overflow: ellipsis`.

3. Two components (MemberCard and LeaderCard) share the same class names
   but apply them differently. One uses a conditional ternary, the other
   uses an unconditional string. A fix to one doesn't propagate.

4. Ellipsis styles are attached to a child selector
   (`.skillsInline .skillTag`) rather than the base class, so any
   `skillTag` not in a `skillsInline` container has no ellipsis ability.

5. Zero test coverage for the teacher card skills layout. Regressions
   only surface through user-reported visual issues.
```

**Why this is wrong:** It looks comprehensive. 5 reasons. Well-structured. But **none of them reference the actual code** in the project. They are all generic observations that could apply to any web project. The real cause — that `.skillTag` block was missing `min-width: 0` after PR #90ad83d introduced a new `.skillTagWrapper` — is not mentioned, because the LLM never scanned the file.

### The Right Answer (With Evidence-First)

```
Scan complete. Found 1 real issue (1 severe):

1. [src/components/TeamSection.module.css:147-168] Phenomenon:
   `.skillTag` has `text-overflow: ellipsis` but the same block is missing
   `min-width: 0`. Per flex spec, flex items have implicit `min-width: auto`
   = min-content, so even with `flex-shrink: 1` the item cannot shrink below
   its min-content width. Ellipsis is therefore silently non-functional.

   Evidence:
   - grep "min-width" in TeamSection.module.css shows it on lines 147-168
     is set in `.skillTagWrapper` (line 144) but not in `.skillTag` (line 151)
   - git log -p shows commit 90ad83d introduced `.skillTagWrapper` with
     `min-width: 0`, but the existing `.skillTag` block was not updated
   - In the production build, on 375px viewport, the third skill tag on
     LeaderCard overflows the card right edge by 87px

Systematic causes (synthesized from the finding above):

1. The flex item "shrink trap" (a flex item's implicit `min-width: auto`
   protects it from shrinking below min-content) is not paired with
   `min-width: 0` on the item itself. This finding in
   TeamSection.module.css:151-168 is the direct instance of this pattern.

   ... (each subsequent cause must reference findings above) ...

Fix recommendations (specific):

- Edit src/components/TeamSection.module.css:151-168: add `min-width: 0;`
  to the `.skillTag` block alongside `flex-shrink: 1`.
- (similar, file-line specific)
```

### Lesson

The wrong answer is "5 structured causes" — it satisfies the user's request structurally but provides zero actionable value. The right answer is "1 real cause with file:line evidence" — it directly tells the developer what to fix.

**Structural fix:** the wrong answer cannot be generated if the LLM is forced to begin with "Scan complete. Found N real issues..." because the user immediately sees whether the LLM actually scanned the code.

---

## Case 2: "Best Practice Lecture" — Also Common

### Context

A developer asks: "Should I split MemberCard into InstructorCard and StudentCard?"

### The Wrong Answer (Without Evidence-First)

```
Splitting is a good idea in general. Here's why:

1. Single Responsibility Principle: Each component should have one reason
   to change. MemberCard currently has 2 reasons (instructor vs student),
   which is a code smell.

2. Easier to test: Smaller components are easier to test in isolation.

3. Better maintainability: Future instructor-specific features can be
   added without affecting student cards.

4. Better reusability: Independent components are more reusable.

5. Industry best practice: Major React projects typically split by role.
```

**Why this is wrong:** The LLM is lecturing about generic React best practices. It has not looked at the actual code, the actual `isInstructor` branches, or whether splitting would create more work than it saves.

### The Right Answer (With Evidence-First)

```
Scan complete. Read 3 files:

- src/components/TeamSection.jsx:10-86: MemberCard has 2 `isInstructor`
  branches (`maxSkills` and `skillsInline` class), only 2 conditional
  behaviors total. Low complexity.
- src/components/TeamSection.jsx:365: only one call site uses
  `isInstructor`, with a ternary at the parent.
- LeaderCard (L86-200) is already a separate component.

Real findings:

1. The current `isInstructor` branches in MemberCard are simple and
   centralized. Splitting would only save ~2 conditional checks.

Recommendation: do NOT split now. The decision should wait until
`isInstructor`-specific behavior grows beyond 2-3 branches, at which
point the refactor pays for itself.
```

### Lesson

The wrong answer gives textbook advice disconnected from the actual codebase. The right answer references the actual file structure and gives a contextual recommendation.

---

## How to Use This File

When you suspect an LLM is giving you the "5 structured causes" pattern, **stop and demand the scan output**. Use this prompt:

> "Before answering, run the tools and show me: which files did you read, what grep patterns did you search, and which `file:line` does each claim reference?"

If the LLM cannot answer that, its previous analysis was pattern matching, not evidence.
