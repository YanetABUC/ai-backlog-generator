# Epic Template

Use this template for every epic. Fill every section before generating child user stories.

**Naming convention:** `epic-{kebab-case-title}.md` (e.g. `epic-vendor-replenishment-automation.md`)

---

```markdown
# Epic: [Short Title]

**Product:** [product name]
**Status:** Draft / In Progress / Complete
**Priority:** P0 / P1 / P2 / P3
**PM:** [name]
**Target Quarter / Release:** [quarter or milestone]

---

## 1. Narrative (Big Cinematic Story)

**In order to** [the strategic business outcome — what the organization achieves],
**As a** [primary user segment — specific role and context, never just "user"],
**I want to** [the high-level capability this epic delivers],
**So that** [the direct, measurable transformation in the user's experience or work].

---

## 2. Strategic Context

**Current situation:**
[Describe how the world works today — the process, tool, or workflow that exists before this epic ships. Be specific: numbers, steps, time, friction points.]

**Core problems:**
- [Problem 1 — specific, observable, with impact if known]
- [Problem 2]
- [Problem 3]

**Operational limitations:**
[What does the current state prevent the organization from doing? What scale, speed, or quality ceiling exists today?]

**Business impact of the status quo:**
[What is the measurable cost of not solving this? Revenue loss, time waste, error rate, compliance risk, customer churn, etc.]

**Why now:**
[What makes this the right time to solve it? Market pressure, regulatory deadline, strategic initiative, user growth, technical readiness, competitive threat?]

---

## 3. Desired Transformation

**What this epic will enable:**
[Describe the future state — what becomes possible that wasn't before. Focus on user capability and business outcome, not features.]

**Shift from → to:**

| From | To |
|---|---|
| [current state — specific] | [future state — specific] |
| [current state] | [future state] |
| [current state] | [future state] |

---

## 4. Scope Definition

### In Scope

**Major capabilities:**
- [Capability 1 — what the user will be able to do]
- [Capability 2]
- [Capability 3]

**Behavioral changes:**
- [How the system's behavior will change — observable, not implementation]
- [Behavioral change 2]

### Out of Scope

*Be explicit. These exclusions prevent scope creep and set clear engineering boundaries.*

- [Explicit exclusion 1 — e.g., "ML-based demand forecasting (Phase 3)"]
- [Explicit exclusion 2]
- [Explicit exclusion 3]

---

## 5. Target Users and Actors

### Primary Beneficiaries (users who directly gain value)

| User | Role & Context | Primary Pain Today | Expected Benefit |
|---|---|---|---|
| [User type 1] | [role + context] | [specific pain] | [specific benefit] |
| [User type 2] | | | |

### Secondary Beneficiaries (users who benefit indirectly)

| User | How they benefit |
|---|---|
| [User type] | [benefit] |

### System Actors (non-human actors involved)

| Actor | Role in this epic |
|---|---|
| [External system / service] | [what it does in this context] |
| [Scheduled job / event] | [what it triggers] |

---

## 6. High-Level Functional Expectations

*The system shall... — observable behaviors, not implementation. These will be refined into Functional Requirements in child user stories.*

- The system shall [observable behavior 1]
- The system shall [observable behavior 2]
- The system shall [observable behavior 3]
- The system shall [error or exception behavior]
- The system shall [role/permission behavior]
- The system shall [auditability behavior]

---

## 7. Non-Functional Expectations

**Performance:**
[Response time, throughput, load expectations at the epic level — e.g., "All replenishment queue views must load within 2 seconds for locations with up to 2,000 SKUs"]

**Security:**
[Access control, data isolation, input handling expectations — e.g., "All replenishment data must be isolated by location; cross-location access is prohibited at every layer"]

**Reliability:**
[Availability, degraded-mode behavior, failure recovery — e.g., "The suggestion queue must remain available even when the urgency scoring service is degraded, with a visible fallback sort order"]

**Scalability:**
[Data volume, user concurrency, growth expectations — e.g., "The epic must support up to 500 concurrent suggestions across 10 locations without SLA degradation"]

**Auditability:**
[Logging, traceability, regulatory requirements — e.g., "Every PO decision (approval, modification, dismissal) must be immutably logged with actor, timestamp, and changed values"]

---

## 8. Constraints and Assumptions

**Constraints:**
- [Constraint 1 — regulatory, technical, organizational, time-based]
- [Constraint 2]
- [Constraint 3]

**Assumptions:**
- [Assumption 1 — something we believe to be true that, if wrong, would change the scope or approach]
- [Assumption 2]
- [Assumption 3]

*Flag each assumption for validation during the Fast Feedback Loop before story generation begins.*

---

## 9. Dependencies

**Internal dependencies (other epics or teams):**

| Dependency | Type | Owner | Status |
|---|---|---|---|
| [Epic or feature that must ship first] | Blocker / Parallel | [team] | Not started / In progress / Done |
| [Shared service or component needed] | Enabler | [team] | |

**External system dependencies:**

| System | Dependency | Risk if unavailable |
|---|---|---|
| [External API / service] | [what we need from it] | [impact] |
| [Data source] | [what data we need] | [impact] |

**Infrastructure / platform dependencies:**

- [e.g., Real-time stock event stream — currently batch; platform team enablement required]
- [e.g., PDF generation service must be provisioned]

---

## 10. Success Metrics

### Business Metrics

| Metric | Baseline | Target | Measurement Method | Timeline |
|---|---|---|---|---|
| [metric 1] | [current value] | [goal] | [how measured] | [when evaluated] |
| [metric 2] | | | | |

### Operational Metrics

| Metric | Baseline | Target | Measurement Method | Timeline |
|---|---|---|---|---|
| [metric 1] | | | | |
| [metric 2] | | | | |

### Technical Metrics

| Metric | Target | Measurement Method |
|---|---|---|
| [e.g., API p95 response time] | [< X ms] | [APM tool] |
| [e.g., Error rate] | [< X%] | [logging dashboard] |

---

## Child Stories Index

*Populated as user stories are generated from this epic.*

| # | Story Title | Size | Status | Sprint |
|---|---|---|---|---|
| US1 | [title] | S/M/L | Draft / Ready / In Progress / Done | |
| US2 | | | | |
```

---

## What This Template Prohibits

Just like user stories, epics describe **what** and **why** — never **how**.

| Prohibited | Reason |
|---|---|
| Technical architecture decisions | Belong in ADRs, written by engineering |
| API design or data model specifications | Engineering concern — epics define behavior, not structure |
| Implementation timelines per story | Sprint planning determines this, not epic definition |
| Vendor or technology selections | Architecture decision, not product scope |

All system behavior belongs in: Functional Expectations, Non-Functional Expectations, Scope Definition — expressed in plain English observable terms.
