## Summary

<!-- 用 1-2 句话描述这次 PR 改了什么、为什么。 -->

> Example: "Adds `docs/common-pitfalls.md` documenting 10 common mistakes when applying the evidence-first rule, based on observed failures in real projects."

## Type of change

<!-- 勾选所有适用的 -->

- [ ] New rule (`rules/*.md`)
- [ ] New integration (`integrations/*/`)
- [ ] Failure case update (`rules/failure-cases.md`)
- [ ] Documentation update (`docs/*.md`)
- [ ] Bug fix (typo, broken link, format issue)
- [ ] Translation
- [ ] Other (describe below)

## Real-world evidence

<!--
证据优先原则：每个 PR 都要有真实证据。

1. 如果是加新规则：附真实失败案例（没有这条规则时的实际输出）
2. 如果是改 bug：附重现步骤
3. 如果是改文档：附读者反馈
-->

### Failure case (if applicable)

What bad output does this PR prevent? Paste actual output here.

### Good case (if applicable)

What does correct output look like after this change? Paste actual output here.

## Checklist

<!--
完成所有项，否则 PR 不会被合并。
参见 CONTRIBUTING.md 了解质量门槛。
-->

### Required

- [ ] I have read [CONTRIBUTING.md](../CONTRIBUTING.md)
- [ ] I have read [the core rule](../rules/evidence-first-analysis.md) to ensure my change is consistent
- [ ] I have added at least one real failure case (for new rules)
- [ ] I have added at least one good example (for new rules)
- [ ] I have updated [CHANGELOG.md](../CHANGELOG.md) (for non-trivial changes)
- [ ] I have added a tool integration file (if applicable)

### Quality bar (per CONTRIBUTING.md)

- [ ] **Evidence-based**: every claim backed by a real example
- [ ] **Concrete and actionable**: a reader can follow without further interpretation
- [ ] **Self-consistent**: no contradictions with other rules

### Style

- [ ] English-first for canonical documents (Chinese translations welcome as `*.zh-CN.md`)
- [ ] No hedging language ("perhaps", "you might want to")
- [ ] No generic `<example>` placeholders — use real file paths
- [ ] Each rule ≤ 500 lines (split if longer)

## Validation

<!--
跑过的检查。GitHub Actions 会跑一部分，但本地能跑最好先跑。
-->

- [ ] Reviewed locally for typos
- [ ] Verified all internal links work (`grep -oE '\]\([^)]+\)' file.md`)
- [ ] For rule changes: ran the self-check from `rules/evidence-first-analysis.md`

## Related issues

<!-- Link related issues: Fixes #123, Related to #456 -->

Closes #
Related to #

## Additional context

<!--
任何其他需要 reviewer 知道的信息：截图、性能数据、相关讨论链接。
-->
