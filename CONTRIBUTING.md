# Contributing to AI Backlog Generator

Thank you for your interest in contributing. This project is a community framework for AI-assisted product backlog generation — contributions that improve its quality, coverage, and usability are welcome.

---

## What We Welcome

- New or improved prompts (new scenarios, edge cases, better phrasing)
- New workflow variations (different discovery methods, team setups)
- New integration examples (tools, AI platforms, team configurations)
- Template improvements (format, clarity, missing sections)
- Bug fixes in documentation (incorrect instructions, broken links, typos)
- New example artifacts (sample epics, stories, evaluations using different domains)

## What We Don't Accept

- Changes that blur the AC vs. BDD distinction — these are intentionally separate
- User stories with Implementation Notes, Technical Design, or Pseudocode
- Prompts that generate low-quality output (vague personas, unmeasurable outcomes, missing scenario types)
- Wholesale rewrites of core templates without prior discussion

---

## How to Contribute

### 1. Fork the repository

Click **Fork** in the top-right corner of the repo page. Work from your fork — do not request direct write access.

### 2. Create a branch

Use a descriptive branch name:

```
git checkout -b add-prompt-rice-scoring
git checkout -b fix-bdd-template-example
git checkout -b workflow-figma-to-stories
```

### 3. Make your changes

- Follow the existing file naming conventions (`kebab-case.md`)
- Keep formatting consistent with existing files
- If adding a new prompt, follow the structure in [`prompts/generate-epics.md`](prompts/generate-epics.md)
- If adding a new example, use the RetailOps domain or introduce a clearly documented new domain

### 4. Open a Pull Request

Push your branch and open a PR against `main`. Fill out the PR template — it asks for the type of change, what was added or modified, and how you tested the output.

PRs without a description will be asked for clarification before review.

---

## Quality Bar for Contributions

All prompts, templates, and examples must meet the same bar as the rest of the framework:

- **Epics** use the 10-section canonical format
- **User stories** use the In order to / As a / I want to / So that format with all required sections
- **Acceptance Criteria** are declarative checklists — no Given/When/Then
- **BDD Scenarios** include all four types: Happy Path, Validation, Edge Case, Failure Handling
- **Personas** are specific roles with context — never "As a user"
- **Outcomes** are measurable — never "so that it works"

If you're unsure whether your contribution meets this bar, run it through [`prompts/evaluate-story-quality.md`](prompts/evaluate-story-quality.md) before submitting.

---

## Discussing Large Changes

For anything beyond a small fix or addition, open an issue first to discuss the direction. This avoids effort spent on PRs that conflict with planned changes.

---

## License

By contributing, you agree that your contributions will be licensed under the [MIT License](LICENSE).
