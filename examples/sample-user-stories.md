# Sample User Stories

**Product:** RetailOps — Vendor Replenishment Automation
**Epic:** Generate and manage purchase order suggestions
**Generated from:** [Sample Epics](sample-epics.md)

Stories follow the canonical template: `US{N}-{kebab-case-title}.md`

---

# US1 — Manager views the PO suggestion queue

## User Story

**In order to** maintain stock levels across all SKUs before vendor order cutoffs,
**As a** warehouse manager responsible for daily inventory replenishment at a single location,
**I want to** view a prioritized list of all pending purchase order suggestions for my location,
**So that** I can review and act on every replenishment need before the daily vendor cutoff — without exporting or searching manually.

## Business Objective

Eliminate the daily 2–3 hour manual review cycle by surfacing all replenishment decisions in a single, prioritized queue — directly contributing to the 80% reduction in manual replenishment effort targeted for 60 days post-launch.

## Problem Context

**Current situation:** Managers export a stock report from the legacy system up to 3 times per day, filter it in Excel to find low-inventory SKUs, and manually determine which items need ordering.

**Pain points:**
- Reports are stale by the time managers review them
- Filtering in Excel is error-prone and time-consuming
- There is no prioritization — all low-stock items look equally urgent
- Managers have no visibility into in-transit stock, leading to over-ordering

**Operational risks:** When a manager is absent, there is no fallback process — stockouts accumulate undetected until the next report export.

**Constraints:**
- Managers are responsible for a single location; they must not see suggestions for other locations
- Managers may oversee 500–2,000 SKUs — the queue must handle this volume without performance degradation

## Desired Outcome

**User impact:** Managers open a single screen each morning and see all replenishment decisions ranked by urgency — no export, no filtering, no manual prioritization.

**Business impact:** Replenishment review time drops from 2–3 hours to under 30 minutes per day.

**Success metrics:**

| Metric | Baseline | Target |
|---|---|---|
| Daily manager time on replenishment review | 2.5 hrs | < 30 min |
| Stockout incidents per month | 42 | < 13 |

## Acceptance Criteria

- AC1: A manager navigating to the Replenishment section sees a queue of all pending suggestions for their assigned location, sorted by urgency (days of stock remaining, ascending)
- AC2: Each suggestion in the queue displays: SKU name, SKU code, current stock level, reorder point, suggested order quantity, suggested vendor, and estimated PO value
- AC3: When the queue contains no pending suggestions, the manager sees a clear empty state message — not a blank screen or a spinner
- AC4: A manager assigned to multiple locations can filter the queue by location, with their primary location selected by default
- AC5: A manager cannot view or act on suggestions belonging to a location they are not assigned to

## Scenarios (BDD)

### Happy Path

```gherkin
Scenario: Manager opens the replenishment queue with pending suggestions
  Given I am a warehouse manager assigned to the Chicago warehouse
  And there are 12 pending PO suggestions for my location
  When I navigate to the Replenishment section
  Then I see a queue of 12 suggestions sorted by urgency (fewest days of stock first)
  And each row displays SKU name, current stock, reorder point, suggested quantity, vendor, and estimated value
```

### Validation

```gherkin
Scenario: Manager without replenishment access attempts to view the queue
  Given I am logged in as a warehouse associate without replenishment permissions
  When I navigate to the Replenishment section URL directly
  Then I see: "You don't have permission to access this page."
  And I am shown a link back to my dashboard
```

### Edge Case

```gherkin
Scenario: Manager is assigned to multiple locations
  Given I am assigned to both the Chicago and Denver warehouses
  When I navigate to the Replenishment queue
  Then the queue defaults to my primary location (Chicago)
  And I can switch to Denver using a location filter — the queue updates without a page reload
  And suggestions from Denver are never shown while Chicago is selected
```

### Failure Handling

```gherkin
Scenario: Data fetch fails on queue load
  Given I navigate to the Replenishment queue
  When the data service returns an error
  Then I see: "Unable to load replenishment data. Please refresh the page."
  And I am shown a Refresh button
  And no partial or stale data is displayed
```

## Functional Requirements

- FR1: The system must display only suggestions with status "Pending" in the queue view; approved, dismissed, and expired suggestions must not appear
- FR2: The system must sort pending suggestions by urgency score (days of stock remaining) in ascending order by default, with the most urgent item at the top
- FR3: The system must restrict the queue to suggestions matching the manager's assigned location(s); cross-location data must never be accessible
- FR4: The system must display a designated empty state when no pending suggestions exist for the selected location
- FR5: The system must update the queue in real time when a suggestion is approved or dismissed — without requiring a page refresh

## Non-Functional Requirements

**Performance:** The queue must load within 2 seconds for a location with up to 2,000 pending suggestions.

**Security:** Location-based data isolation must be enforced server-side; client-side filtering alone is not sufficient.

**Reliability:** If the urgency score calculation service is unavailable, the queue must still load with suggestions sorted by creation date and a visible notice: "Urgency ranking temporarily unavailable."

**Auditability:** Each queue view session is not individually logged, but all actions taken on suggestions (approve, dismiss, modify) are logged with actor, timestamp, and suggestion ID.

**Scalability:** The queue must remain within performance SLA for locations with up to 2,000 SKUs and up to 500 concurrent suggestions.

## Out of Scope

- Bulk selection or bulk approval of multiple suggestions from the queue
- Exporting the queue to CSV or Excel from this view
- Displaying in-transit inventory data alongside stock levels (Phase 2)
- Notification or alert badges on the navigation menu (Phase 2)

## Media

- **Design:** [Figma — Replenishment Queue screen]
- **Prototype:** [link or TBD]
- **Research:** [Warehouse manager time study — Q1 2025]

---

# US2 — System generates a PO suggestion when stock falls below reorder point

## User Story

**In order to** ensure no low-stock SKU goes unactioned because a manager missed a report cycle,
**As a** warehouse manager responsible for maintaining stock levels,
**I want to** have the system automatically create a purchase order suggestion when a SKU's stock falls below its configured reorder point,
**So that** every replenishment need surfaces in my queue without requiring me to actively monitor stock levels.

## Business Objective

Replace the reactive, report-driven replenishment process with a proactive, event-driven one — directly targeting the 70% reduction in stockout incidents within 60 days of launch.

## Problem Context

**Current situation:** Managers identify low-stock items by filtering an Excel export. The export runs 3 times per day, meaning a SKU can be below its reorder point for up to 8 hours before a manager sees it.

**Pain points:**
- Delayed detection means vendors receive late orders, extending lead times
- Managers on leave or out sick leave no monitoring in place
- No prioritization exists — all low-stock items require manual judgment

**Operational risks:** An 8-hour detection lag means SKUs with short lead times can reach zero stock before a PO is placed. This directly causes the 42 monthly stockout incidents.

**Constraints:**
- A suggestion must not be duplicated if one already exists for the same SKU in Pending status
- SKUs without a configured reorder point must not generate suggestions

## Desired Outcome

**User impact:** Managers open their queue and see all replenishment needs already identified — no manual scanning required.

**Business impact:** Detection lag drops from up to 8 hours to under 5 minutes, giving managers maximum lead time to act before vendor cutoffs.

**Success metrics:**

| Metric | Baseline | Target |
|---|---|---|
| Stock detection lag | Up to 8 hours | < 5 minutes |
| Stockout incidents per month | 42 | < 13 |

## Acceptance Criteria

- AC1: When a SKU's stock level is updated and falls at or below its configured reorder point, a PO suggestion with status "Pending" is created automatically
- AC2: The generated suggestion includes: the SKU's primary vendor, the SKU's configured reorder quantity, and an urgency score based on days of stock remaining
- AC3: If a Pending suggestion already exists for the same SKU, no duplicate suggestion is created — the existing suggestion's urgency score is updated instead
- AC4: If a SKU has no configured reorder point, no suggestion is generated when its stock changes
- AC5: If a SKU has no configured primary vendor, a suggestion is still created — with a flag indicating vendor assignment is required before the PO can be approved

## Scenarios (BDD)

### Happy Path

```gherkin
Scenario: Stock update triggers a new PO suggestion
  Given the SKU "BOLT-M6-SS" has a reorder point of 500 units and a primary vendor configured
  And its current stock level is 520 units
  When a stock movement reduces the level to 498 units
  Then a PO suggestion is created with status "Pending"
  And the suggestion shows: vendor = primary vendor, quantity = configured reorder quantity, urgency score = days of stock remaining
  And the suggestion appears in the manager's queue for the SKU's location
```

### Validation

```gherkin
Scenario: Stock update on a SKU with no reorder point configured
  Given the SKU "MISC-TAPE-01" has no reorder point configured
  When a stock movement reduces its level to 5 units
  Then no PO suggestion is created
  And no alert or notification is generated
```

### Edge Case

```gherkin
Scenario: Stock falls below reorder point when a Pending suggestion already exists
  Given a Pending suggestion already exists for SKU "BOLT-M6-SS"
  When another stock movement reduces the level further
  Then no second suggestion is created
  And the urgency score on the existing suggestion is recalculated to reflect the updated stock level
```

### Failure Handling

```gherkin
Scenario: Suggestion creation fails due to a system error
  Given a stock update triggers the suggestion creation process
  When the suggestion cannot be saved due to a database error
  Then the failure is logged with: SKU ID, timestamp, stock level, and error detail
  And a retry is attempted within 60 seconds
  And if the retry also fails, the on-call alert is triggered with the logged details
```

## Functional Requirements

- FR1: The system must evaluate every stock level update against the SKU's configured reorder point and create a suggestion when stock is at or below that point
- FR2: The system must calculate an urgency score for each suggestion, defined as: current stock level divided by average daily consumption over the past 30 days, expressed as days of stock remaining
- FR3: The system must prevent duplicate suggestions — if a Pending suggestion exists for a SKU, the system must update its urgency score rather than creating a new record
- FR4: The system must create suggestions with a "Vendor Required" flag for SKUs that have no primary vendor configured, and these suggestions must be visually differentiated in the manager's queue
- FR5: The system must not create suggestions for SKUs that have no reorder point configured

## Non-Functional Requirements

**Performance:** Suggestion creation must complete within 5 seconds of the triggering stock update event.

**Security:** Suggestion creation is a system-initiated action — it must not be triggerable by a direct user request or an external API call without appropriate authorization.

**Reliability:** If the suggestion creation process fails, it must retry at least once before logging a failure; no stock update event may be silently dropped.

**Auditability:** Every suggestion creation event must be logged with: triggering event ID, SKU ID, stock level at time of trigger, reorder point value, and timestamp.

**Scalability:** The suggestion creation process must handle up to 1,000 concurrent stock update events without degradation in the 5-second SLA.

## Out of Scope

- Machine learning or demand forecasting for dynamic reorder point recommendations (Phase 3)
- Multi-vendor suggestion (suggesting an alternative vendor when the primary is unavailable)
- Suggestions triggered by forecasted depletion rather than actual stock levels

## Media

- **Design:** N/A — system-initiated process, no UI in this story
- **Prototype:** N/A
- **Research:** [Stockout root cause analysis — Q4 2024]
