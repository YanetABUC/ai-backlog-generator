# Prompt: Generate Epics

Use this prompt to generate product epics from a validated problem statement or product brief. Output follows the canonical epic template.

---

## When to Use

- After completing discovery and validating assumptions
- When you have a product brief and need to structure the work
- When breaking a large initiative into shippable increments

---

## Core Prompt

```
You are a senior product manager generating product epics from a product brief or validated problem statement.

## Epic Format

Every epic must follow this exact structure:

---
# Epic: [Short Title]

## 1. Narrative (Big Cinematic Story)
**In order to** [strategic business outcome],
**As a** [primary user — specific role and context],
**I want to** [the high-level capability this epic delivers],
**So that** [measurable transformation in the user's experience or work].

## 2. Strategic Context
**Current situation:** [how the world works today — specific, with numbers if possible]
**Core problems:** [bullet list — specific, observable, with impact]
**Operational limitations:** [what the current state prevents]
**Business impact of the status quo:** [measurable cost of not solving this]
**Why now:** [market, regulatory, strategic, or technical trigger]

## 3. Desired Transformation
**What this epic will enable:** [future state — capability and outcome]
**Shift from → to:** [table: From | To — specific pairs]

## 4. Scope Definition
### In Scope
[Major capabilities + behavioral changes the system will exhibit]
### Out of Scope
[Explicit exclusions — prevent scope creep]

## 5. Target Users and Actors
[Primary beneficiaries, secondary beneficiaries, system actors — with context and expected benefit]

## 6. High-Level Functional Expectations
[The system shall... — observable behaviors, not implementation]

## 7. Non-Functional Expectations
[Performance / Security / Reliability / Scalability / Auditability — measurable where possible]

## 8. Constraints and Assumptions
[Regulatory, technical, organizational limits + flagged assumptions to validate]

## 9. Dependencies
[Internal epics/teams, external systems, infrastructure — with owner and status]

## 10. Success Metrics
[Business, operational, and technical metrics with baseline, target, and measurement method]
---

## CRITICAL CONSTRAINTS

- Never include: architecture decisions, API specifications, data model design, technology selections
- Epics describe WHAT and WHY — never HOW
- Narrative must use the exact format: In order to / As a / I want to / So that
- "As a" must be a specific persona — never "user," "admin," or "team"
- "In order to" is the organization's strategic gain — not the user's benefit (that's "So that")
- Every assumption must be flagged for validation
- Out of Scope must be explicit, not "everything else"

## Input

**Product initiative:** [describe what you're building]
**Problem statement:** [what problem, for whom]
**Target users:** [who specifically]
**Business goal:** [measurable success]
**Constraints:** [time, budget, technical, regulatory]
**Out of scope for this initiative:** [explicitly excluded]
**Why now:** [trigger or urgency]

Generate [N] epics. Each epic should represent 2–6 weeks of work for a small team and deliver standalone value.
```

---

## Variant: Epic Generation With RICE Prioritization

Add this after generating epics to rank them:

```
After generating all epics, rank them using RICE scoring:

- Reach: Users affected per quarter (1=handful, 5=all users)
- Impact: How significantly this improves their experience (0.25=minimal, 3=massive)
- Confidence: How confident we are in our estimates (50%=low, 100%=high)
- Effort: Person-months to deliver

RICE Score = (Reach × Impact × Confidence) / Effort

Present as a ranked table. For each score, provide one-sentence justification.
```

---

## Variant: Epic Generation From Codebase Gap Analysis

```
Based on the following codebase gap analysis, generate epics for the identified gaps.

Each epic should:
- Map to one or more gaps in the Scope Definition section
- Reference existing capabilities in the Strategic Context ("Current situation")
- List codebase-identified constraints in Section 8 (Constraints and Assumptions)
- NOT include implementation details — express gaps as capability statements, not code changes

Codebase gap analysis:
[paste output from Workflow 03, Steps 4–6]

Product vision:
[paste brief]
```

---

## Example Output

```markdown
# Epic: Enable managers to monitor stock levels and act on replenishment needs

## 1. Narrative (Big Cinematic Story)
**In order to** prevent stockout incidents that currently cost $120K/month in lost revenue,
**As a** warehouse manager responsible for daily inventory replenishment across 500–2,000 SKUs,
**I want to** have a real-time, prioritized view of all replenishment needs with one-click action,
**So that** I spend under 30 minutes per day on replenishment decisions — down from 2.5 hours — and never miss a critical reorder.

## 2. Strategic Context
**Current situation:** Managers export a stock report 3 times per day from the legacy system, filter it in Excel to find low-inventory SKUs, and manually create POs in the vendor portal — one vendor at a time, 8–15 minutes per PO.

**Core problems:**
- Detection lag of up to 8 hours between a SKU falling below reorder point and manager awareness
- Manual PO creation takes 8–15 minutes per vendor per day — unscalable
- No prioritization: all low-stock items require equal manual judgment
- No audit trail: no record of who ordered what, when, or why

**Operational limitations:** The current process cannot scale beyond the existing manager headcount. A single absent manager causes stockouts to accumulate undetected.

**Business impact of the status quo:** 42 stockout incidents per month cost an estimated $120K in lost sales. Each incident also generates 3–5 support tickets and erodes customer trust.

**Why now:** A new warehouse management system migration creates a technical window to replace the legacy report process. The business has set a Q2 OKR to reduce stockouts by 70%.

## 3. Desired Transformation
**What this epic will enable:** Managers will open a single screen each morning, see every replenishment need ranked by urgency, and approve or dismiss PO suggestions in seconds — with a full audit trail and no manual data entry.

**Shift from → to:**
| From | To |
|---|---|
| Manual Excel report filtering, 3x/day | Real-time suggestion queue, always current |
| 8–15 min per PO, manual vendor portal entry | One-click approval, document auto-generated |
| No prioritization | Urgency score: days of stock remaining |
| Zero audit trail | Immutable log of every replenishment decision |
| Up to 8-hour detection lag | Detection within 5 minutes of stock movement |

## 4. Scope Definition
### In Scope
**Major capabilities:**
- Real-time stock monitoring against configurable reorder points per SKU
- Automated PO suggestion creation when stock falls at or below reorder point
- Manager review queue with approve, modify, and dismiss actions
- PO document generation on approval (downloadable PDF)
- Routing of POs ≥ $5,000 to procurement lead for approval

**Behavioral changes:**
- The system acts on stock movements in real time — not on a scheduled export cycle
- PO suggestions are prioritized by urgency score (days of stock remaining)
- Every replenishment action creates an immutable audit log entry

### Out of Scope
- Automated PO submission to vendor APIs (Phase 2)
- Machine learning or demand forecasting for reorder point recommendations (Phase 3)
- Integration with financial systems (SAP, NetSuite)
- In-transit inventory visibility

## 5. Target Users and Actors
### Primary Beneficiaries
| User | Role & Context | Primary Pain Today | Expected Benefit |
|---|---|---|---|
| Warehouse Manager | Manages 500–2,000 SKUs at one location; non-technical; works from desktop and tablet | 2.5 hrs/day on manual review and PO entry | < 30 min/day; no missed reorder points |

### Secondary Beneficiaries
| User | How they benefit |
|---|---|
| Procurement Lead | Receives routed high-value POs with full context; no email chain required |

### System Actors
| Actor | Role in this epic |
|---|---|
| Stock movement event stream | Triggers suggestion creation in real time |
| Vendor catalog service | Provides primary vendor and pricing for PO suggestions |
| PDF generation service | Produces downloadable PO documents on approval |

## 6. High-Level Functional Expectations
- The system shall create a PO suggestion within 5 minutes of a stock level falling at or below a SKU's configured reorder point
- The system shall present suggestions in a queue sorted by urgency (days of stock remaining, ascending)
- The system shall allow a manager to approve a suggestion with a single confirm action
- The system shall generate a downloadable PO document within 10 seconds of approval for POs below the approval threshold
- The system shall route approved POs at or above the configured threshold to the procurement lead for review
- The system shall prevent duplicate suggestions for the same SKU while a Pending suggestion exists
- The system shall log every replenishment action (suggestion creation, approval, modification, dismissal) immutably

## 7. Non-Functional Expectations
**Performance:** Suggestion queue must load within 2 seconds for locations with up to 2,000 pending suggestions. Suggestion creation must complete within 5 minutes of the triggering stock event.

**Security:** Location-based data isolation must be enforced server-side. Managers may only view and act on suggestions within their assigned locations.

**Reliability:** The suggestion queue must remain accessible in a degraded mode (sorted by creation date) if the urgency scoring service is unavailable.

**Scalability:** The system must handle up to 1,000 concurrent stock update events and support up to 10 locations with 2,000 SKUs each.

**Auditability:** Every state change on a PO suggestion must be immutably logged with: actor, timestamp, previous state, new state, and any changed values.

## 8. Constraints and Assumptions
**Constraints:**
- Vendor API integration is out of scope for Phase 1 — POs will be generated as downloadable documents only
- The PO approval threshold ($5,000) is configurable but requires admin access to change
- The system must integrate with the existing RetailOps product catalog and stock level data — no new data sources

**Assumptions:**
- [To validate] Real-time stock event stream can be enabled by the platform team within the project timeline
- [To validate] SKU master data includes primary vendor and reorder quantity fields — or these can be added
- [To validate] The PDF generation service already deployed for invoices can be reused for PO documents

## 9. Dependencies
| Dependency | Type | Owner | Status |
|---|---|---|---|
| Real-time stock event stream | Blocker | Platform team | Not started |
| Vendor catalog API (pricing, minimum order qty) | Blocker | Integrations team | In progress |
| PDF generation service | Enabler | Platform team | Available |
| Procurement Lead role provisioning | Parallel | IT/Admin | Not started |

## 10. Success Metrics
### Business Metrics
| Metric | Baseline | Target | Measurement Method | Timeline |
|---|---|---|---|---|
| Stockout incidents per month | 42 | < 13 | Inventory system | 60 days post-launch |
| Manager time on replenishment per day | 2.5 hrs | < 30 min | Time tracking / self-report | 30 days post-launch |

### Operational Metrics
| Metric | Baseline | Target | Measurement Method | Timeline |
|---|---|---|---|---|
| % POs with full audit trail | 0% | 100% | Audit log completeness check | Launch day |
| PO suggestion acceptance rate | — | > 80% | Suggestion outcome tracking | 30 days post-launch |

### Technical Metrics
| Metric | Target | Measurement Method |
|---|---|---|
| Suggestion creation latency | < 5 min from stock event | Event processing dashboard |
| Queue load time (p95) | < 2 seconds | APM tool |
| Suggestion creation error rate | < 0.1% | Error log dashboard |
```

---

## Tips for Better Output

- Include user research data in the prompt ("our top support request is X" → becomes Strategic Context)
- Name the constraints explicitly — AI will scope epics more realistically
- For multi-epic initiatives, generate all epics first, then RICE-score before generating stories
- **Before generating user stories, resolve every `[To validate]` assumption in Section 8 of each epic.** Each unresolved assumption affects story scope and acceptance criteria. Stories written against unvalidated assumptions may need full rewrites after the answer surfaces.
- If you get epics that are too technical, add: "Express all scope as user-facing capabilities and behavioral changes — no technical implementation details"
