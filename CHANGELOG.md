# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Planned
- Additional tool integrations (Cody-specific file, Windsurf, etc.)
- Failure case submissions from real users
- Translations (Spanish, Japanese, German)

## [1.1.0] - 2026-07-04

### Added — Phase 2: Production-Ready Open Source Project

This release transforms the repository from a "rule + integrations" snapshot into a **production-ready open-source project** with full contribution infrastructure.

**Documentation**
- `docs/extending-rules.md` — step-by-step guide to add a new rule using the 6-section template
- `docs/common-pitfalls.md` — 10 common mistakes when applying the evidence-first rule (with anti-examples and detection signals)
- `rules/design-rationale.md` — why the rule is structured this way (forcing function, evidence discipline, anti-pattern visibility)

**Community Infrastructure**
- `CONTRIBUTING.md` — full contribution guide (quality bar, review process, PR template for new rules)
- `CHANGELOG.md` (this file) — release history following Keep a Changelog
- `.github/PULL_REQUEST_TEMPLATE.md` — PR template enforcing the contribution checklist
- `.github/ISSUE_TEMPLATE/bug_report.yml` — bug report form
- `.github/ISSUE_TEMPLATE/feature_request.yml` — feature request form (new rules, integrations, translations)
- `.github/ISSUE_TEMPLATE/question.yml` — question form

**Per-integration READMEs**
- `integrations/trae/README.md` — Trae install + verification steps
- `integrations/cursor/README.md` — Cursor install + FAQ
- `integrations/claude-code/README.md` — Claude Code install + slash command integration
- `integrations/github-copilot/README.md` — Copilot install + limitations
- `integrations/aider/README.md` — Aider install + config file
- `integrations/continue/README.md` — Continue/Cody install + config examples

**Automation**
- `.github/workflows/validate.yml` — CI workflow validating:
  - Required files (README, LICENSE, canonical rule) exist
  - All 6 required sections present in `rules/evidence-first-analysis.md`
  - `rules/failure-cases.md` has at least 1 case
  - CHANGELOG updated when `rules/` changes
  - Integration files have installation notes

### Fixed
- README Repository Structure now reflects the full file tree (was outdated relative to Phase 1)
- Removed empty `test.md` test residue

## [1.0.0] - 2026-07-04

### Added — Phase 1: Core Rule and Integrations

- `README.md` — bilingual project entry, 5-minute setup
- `LICENSE` — MIT
- `rules/evidence-first-analysis.md` — the canonical rule (When to Apply / Core Discipline / Mandatory Procedure / Output Structure / Anti-Patterns / Self-Check)
- `rules/failure-cases.md` — real failure examples
- `integrations/trae/SKILL.md` — Trae IDE Skill format
- `integrations/cursor/.cursorrules` — Cursor format
- `integrations/claude-code/CLAUDE.md` — Claude Code format
- `integrations/github-copilot/copilot-instructions.md` — GitHub Copilot format
- `integrations/aider/CONVENTIONS.md` — Aider format
- `integrations/continue/.continuerules` — Continue.dev / Cody format
- `prompts/full-analysis.md` — copy-paste prompt (English + Chinese)
- `examples/good-bad-comparison.md` — side-by-side good vs bad examples

### Design Principles
- **Cross-agent**: same rule, 6 tool-specific format wrappers
- **Bilingual**: core documents in English with Chinese summaries
- **MIT licensed**: permissive open source
- **Failure-case driven**: real examples block real failure modes
- **Self-check enforced**: every response must pass a checklist before sending

[Unreleased]: https://github.com/ztluck78/llm-agent-rules/compare/v1.1.0...HEAD
[1.1.0]: https://github.com/ztluck78/llm-agent-rules/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/ztluck78/llm-agent-rules/releases/tag/v1.0.0
