# Extending the Ruleset

> How to add a new rule that fits the existing evidence-first pattern.
> 如何添加新规则，保持与现有 evidence-first 模式一致。

This guide is for contributors who want to add a new rule to the ruleset. It explains what makes a rule fit, what breaks the pattern, and the concrete steps to follow.

## When to Add a New Rule

A new rule is justified when:

1. **There is a recurring failure mode** that the existing rules don't block
2. **The failure is structural**, not stylistic — i.e. it can be blocked by a forcing function
3. **The rule can be applied selectively** — not all questions should trigger it

A new rule is **not** justified when:

- The issue can be solved by a small addition to an existing rule
- The issue is purely a style preference (e.g. "prefer TypeScript over JavaScript")
- The rule would force a specific answer (e.g. "always use Tailwind") rather than a process

## The 6-Section Template

Every rule in this repository follows the same 6-section structure. This is not arbitrary — each section blocks a specific failure mode. New rules should follow the same template.

### 1. When to apply

List trigger keywords and question patterns. Be specific.

**Good:**
```
Apply when the user asks:
- "为什么反复出这个问题"
- "audit / investigation / review"
- "why does this keep happening"
```

**Bad:**
```
Apply when the user is asking analysis questions.
```

The good version has clear trigger conditions. The bad version is too vague to be useful.

### 2. Core discipline

A single sentence stating the central obligation. This is the rule's "north star" — every other section is a way to enforce this.

**Good:**
```
Call tools to scan the code first, gather real evidence, then analyze.
```

**Bad:**
```
Be thorough and consider all angles.
```

The good version is testable. The bad version is not.

### 3. Mandatory procedure

The step-by-step actions the LLM must take. At least 3 categories, each with concrete actions.

Example categories:
- Read key files (specific list)
- Run project checks (specific commands)
- Search for patterns (specific keywords)
- Read tool outputs verbatim (no paraphrasing)

Each category should have 2-4 specific actions.

### 4. Output structure

A format template the LLM must follow. Each part of the template should be **testable** — the LLM should be able to check "did I include this section?"

**Good template:**
```
- [file:line] Phenomenon: ...
  Evidence: ...
  Severity: High / Medium / Low
```

**Bad template:**
```
Describe the findings clearly.
```

### 5. Anti-patterns

Explicit list of failure modes the rule blocks. Each anti-pattern should be a **specific** failure, not a general one.

**Good:**
```
- "Give 5 reasons first, then add evidence as an afterthought"
- Listing best practices disconnected from the code
- Skipping the scan and saying 'I think...'
```

**Bad:**
```
- Don't be sloppy
- Don't ignore details
```

### 6. Self-check

A 3-5 item checklist the LLM must pass before sending. Each item should be a literal yes/no question.

**Good:**
```
- [ ] I invoked at least 3 tools
- [ ] Every claim references file:line
- [ ] I did not write "this kind of problem is usually caused by X"
```

**Bad:**
```
- [ ] Be thorough
- [ ] Be accurate
```

## The Failure-Case Requirement

**Every new rule must include at least one real failure case** — an example of bad output that the rule would have blocked.

Why this is mandatory:

1. **Tests understanding**: if you can't produce a concrete failure, you don't fully understand the problem
2. **Demonstrates impact**: a failure case shows the cost of not following the rule
3. **Communicates intent**: a reviewer can quickly see what the rule prevents

A failure case has three parts:
- **Context**: what was the user asking?
- **Bad output**: what did the LLM produce (verbatim, ideally)?
- **Lesson**: what would the rule have changed?

If your "failure case" is hypothetical ("imagine the LLM says X"), it doesn't count. Use a real observed case or don't add the rule.

## The Good Example Requirement

Similarly, every new rule should include at least one **good example** — what correct output looks like.

This is the counterweight to failure cases. Without good examples, reviewers can't tell if the rule is **achievable** or just **aspirational**.

A good example has:
- Realistic input (similar to what users actually ask)
- Realistic output (what a well-applied rule produces)
- Note of where the structure came from (which sections of the rule)

## Naming and File Structure

Place new rules in `rules/` with a kebab-case filename:

```
rules/
├── evidence-first-analysis.md     # existing
├── evidence-first-analysis.zh-CN.md  # translation (optional)
└── <your-new-rule>.md
```

Update the main `README.md` to link to the new rule. Update `CHANGELOG.md`.

## Adding a New Tool Integration

When adding a new AI tool integration:

1. Create `integrations/<tool-name>/<config-file>` (e.g. `integrations/zed/.zeds_rules`)
2. Create `integrations/<tool-name>/README.md` documenting how to use it
3. The content of the config file should be a near-copy of `rules/evidence-first-analysis.md`, adapted to the tool's syntax
4. Link from the main README's "5-Minute Setup" table

Tool integration files should:
- **Be self-contained** — a user copying the file gets the full rule
- **Adapt to the tool's syntax** — comment style, frontmatter requirements
- **Link to the canonical version** for the source of truth

## Common Mistakes

### Mistake 1: Adding best practices to a rule

Don't do:
```
Rule section: "Best Practices"
- Always use TypeScript
- Always write tests
- Always use the repository pattern
```

This contradicts the rule's own anti-pattern. Best practices disconnected from the code are exactly what the rule blocks. If a best practice is relevant, it should be in a finding, with a specific `file:line` reference.

### Mistake 2: Too many sections

Don't add a 10-section rule with subsections. The 6-section template exists because 6 is the sweet spot. More sections make the rule harder to follow.

### Mistake 3: Vague anti-patterns

Don't write:
```
- Don't be generic
- Don't be vague
```

These are not testable. Write:
```
- Don't say "this kind of problem is usually caused by X"
- Don't list best practices without file:line
```

### Mistake 4: No real failure case

If the failure case is hypothetical, the rule is untested. Either:
- Find a real observed case (in your own work or in the issue tracker)
- Don't add the rule yet — wait until you have evidence

### Mistake 5: Long rules

If a rule exceeds 500 lines, it's probably doing too much. Either:
- Split it into multiple rules
- Move detailed examples to a separate document

## Review Checklist

Before submitting a PR for a new rule, run through this list:

- [ ] 6 sections present and well-structured
- [ ] At least 1 real failure case
- [ ] At least 1 good example
- [ ] Design rationale (1-2 paragraphs)
- [ ] Self-check is testable
- [ ] File follows naming convention
- [ ] CHANGELOG.md updated
- [ ] At least 1 tool integration added (if applicable)
- [ ] No best-practices section
- [ ] No more than 500 lines

## Getting Help

If you're unsure whether a new rule is justified, open an issue with:
- The failure mode you've observed
- An example of bad output
- Your proposed rule

A maintainer will help you decide whether to proceed, refine, or fold into an existing rule.
