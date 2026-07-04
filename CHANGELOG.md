# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Planned
- `docs/extending-rules.md` — how to add new rules
- `docs/common-pitfalls.md` — common mistakes when applying the rule
- GitHub Actions validation workflow
- PR / issue templates

## [1.0.0] - 2026-07-04

### Added
- `README.md` — bilingual project entry, 5-minute setup
- `LICENSE` — MIT
- `rules/evidence-first-analysis.md` — the canonical rule
- `rules/failure-cases.md` — real failure examples
- `rules/design-rationale.md` — why the rule is structured this way
- `integrations/trae/SKILL.md` — Trae IDE Skill format
- `integrations/cursor/.cursorrules` — Cursor format
- `integrations/claude-code/CLAUDE.md` — Claude Code format
- `integrations/github-copilot/copilot-instructions.md` — GitHub Copilot format
- `integrations/aider/CONVENTIONS.md` — Aider format
- `integrations/continue/.continuerules` — Continue.dev format
- `prompts/full-analysis.md` — copy-paste prompt (English + Chinese)
- `examples/good-bad-comparison.md` — side-by-side good vs bad examples

### Design Principles
- **Cross-agent**: same rule, 5+ tool-specific format wrappers
- **Bilingual**: core documents in English with Chinese summaries
- **MIT licensed**: permissive open source
- **Failure-case driven**: real examples block real failure modes
- **Self-check enforced**: every response must pass a checklist before sending

[Unreleased]: https://github.com/ztluck78/llm-agent-rules/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/ztluck78/llm-agent-rules/releases/tag/v1.0.0
