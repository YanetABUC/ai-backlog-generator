# Definition of Done

This file is read by the backlog skills during story generation, evaluation, refinement, and dev-ready handoff. Edit it to reflect your team's standards. Skills that generate or refine stories will use it to shape AC and NFRs. Skills that evaluate or hand off stories will check compliance against it.

---

## Required AC Coverage

Every user story must include acceptance criteria that explicitly cover each of the following. Skills will add or flag missing coverage during generation and refinement.

- [ ] Happy path (primary success case)
- [ ] Validation failure (invalid or missing input)
- [ ] System failure or API error (what the user sees and whether data is preserved)
- [ ] Permission denied (what happens when an unauthorized user attempts the action)
- [ ] Empty state (when no data is available to display)

> Add team-specific items below this line. Example: "Analytics event fired on success."

---

## Required NFR Standards

When any of the following apply to a story, it must include an NFR entry with the specified threshold. Skills will use these as defaults when generating or refining NFRs.

- **Performance:** Page or action must complete within [X seconds] under normal load
- **Security:** Role-based access must be enforced server-side for all data operations
- **Accessibility:** Interactive elements must meet WCAG 2.1 AA
- **Auditability:** User actions that modify data must be logged with actor, timestamp, and changed values

> Replace placeholder values and remove lines that do not apply to your product.

---

## Delivery Gates

Answered during dev-ready handoff. Skills will use this section to generate the DoD checklist instead of the default hardcoded list.

- Unit tests required: yes / no
- Integration tests required: yes / no
- QA sign-off required: yes / no
- Analytics event required for user-facing features: yes / no — naming pattern: [pattern or N/A]
- Feature flag required for new features: yes / no
- Design review sign-off required: yes / no
