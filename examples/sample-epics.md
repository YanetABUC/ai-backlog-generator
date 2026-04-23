# Sample Epics

**Product:** RetailOps — Vendor Replenishment Automation  
**Generated from:** [Sample Product Brief](sample-product-brief.md)

---

## Epic 1: Monitor stock levels and trigger replenishment alerts

**User segment:** Warehouse managers responsible for 500–2,000 SKUs per location  
**Business goal:** Eliminate blind spots in stock visibility so managers act before stockouts occur  
**Success metric:** Stockout incidents reduced by 70% within 60 days of launch  
**In scope:** Real-time stock monitoring, configurable reorder points per SKU, in-app and email alert when stock falls below threshold  
**Out of scope:** ML-based forecasting, demand prediction, automatic PO creation (Epic 2)  
**Dependencies:** Existing stock level data API must expose real-time data (currently 3x/day batch — requires platform team to enable real-time feed)  

**Example stories:**
- Manager configures reorder point for a specific SKU
- Manager configures reorder points for an entire product category
- System sends in-app alert when SKU stock falls below reorder point
- Manager receives email digest of low-stock items at 7am and 1pm
- Manager views dashboard of all SKUs currently below reorder point
- Manager filters low-stock alerts by location

---

## Epic 2: Generate and manage purchase order suggestions

**User segment:** Warehouse managers reviewing daily replenishment needs  
**Business goal:** Reduce PO creation time from 8–15 min/vendor to under 2 min by pre-populating suggestions  
**Success metric:** 80% of PO suggestions accepted without modification within 30 days of launch  
**In scope:** Automatic PO suggestion creation when stock triggers alert, manager review queue (approve/modify/dismiss), PO document generation for download  
**Out of scope:** Direct API submission to vendor portals (Phase 2), automated approval without review  
**Dependencies:** Epic 1 (stock alerts) must be complete; vendor catalog data must include pricing and minimum order quantities  

**Example stories:**
- System generates a PO suggestion when a SKU falls below reorder point
- Manager views the PO suggestion queue for their location
- Manager approves a PO suggestion without modification
- Manager modifies quantity or vendor on a PO suggestion before approving
- Manager dismisses a PO suggestion with a reason
- System generates a PO document (PDF) when a suggestion is approved
- PO document includes: vendor info, line items, quantities, total value, requestor, date

---

## Epic 3: Route large purchase orders for procurement approval

**User segment:** Procurement leads managing multi-location spend oversight  
**Business goal:** Replace email-based approval chain with tracked, auditable workflow — eliminating lost approvals and reducing approval time from 24–48 hrs to under 4 hrs  
**Success metric:** 100% of POs over $5K routed through system approval (0 email approvals) within 30 days  
**In scope:** Automatic routing of POs ≥ $5K to procurement lead, in-app and email notification, approve/reject with notes, manager notification of decision  
**Out of scope:** Multi-level approval chains, conditional routing beyond the $5K threshold  
**Dependencies:** Epic 2 (PO suggestion generation) must be complete; procurement lead role must be provisioned in the system  

**Example stories:**
- System routes PO to procurement lead when approved PO value exceeds $5,000
- Procurement lead receives in-app notification and email for pending approval
- Procurement lead approves a PO with optional notes
- Procurement lead rejects a PO with a required rejection reason
- Manager receives notification when their PO is approved or rejected
- Procurement lead views all pending approvals across locations

---

## Epic 4: Maintain a full audit trail for all replenishment actions

**User segment:** Procurement leads and compliance stakeholders reviewing historical decisions  
**Business goal:** Create a complete, immutable record of every replenishment decision to support audits and vendor disputes  
**Success metric:** 100% of POs have full audit trail (who created, who approved, timestamps, any modifications)  
**In scope:** Audit log for all PO suggestion creation, approval, modification, dismissal, and rejection; export to CSV  
**Out of scope:** ERP integration, financial system sync  
**Dependencies:** Epics 2 and 3 must be complete (audit log captures their events)  

**Example stories:**
- System logs an audit event for every PO state transition (suggested, approved, modified, dismissed, rejected)
- Procurement lead views the complete audit trail for a specific PO
- Manager views the history of replenishment actions for a specific SKU
- Procurement lead exports the audit trail for a date range as a CSV
- Audit log entries are immutable — no record can be edited or deleted

---

## Epic Priority (RICE-scored)

| Epic | Reach | Impact | Confidence | Effort | RICE Score |
|---|---|---|---|---|---|
| Epic 1: Stock monitoring + alerts | 5 | 3 | 80% | 3 | 4.0 |
| Epic 2: PO suggestion generation | 5 | 3 | 80% | 5 | 2.4 |
| Epic 3: Procurement approval routing | 3 | 2 | 90% | 3 | 1.8 |
| Epic 4: Audit trail | 2 | 1 | 100% | 2 | 1.0 |

**Recommended delivery order:** Epic 1 → Epic 2 → Epic 3 → Epic 4  
Epic 4 can begin in parallel with Epic 3 (minimal dependency).
