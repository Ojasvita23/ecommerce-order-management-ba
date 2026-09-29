# Stakeholder Analysis — OMS v2

## Purpose
Identify who is affected by, or has influence over, the OMS v2 project, and define an appropriate engagement strategy for each.

## Stakeholder Table

| Stakeholder | Role in Project | Power | Impact | Specific Needs / Concerns | Engagement Strategy |
|---|---|---|---|---|---|
| Business Owner | Sponsor, final approver | High | High | Scale orders without proportional support cost; measurable ROI | Manage closely, early |
| Product Manager | Owns scope/priorities | High | High | Clear, prioritised requirements; alignment with business goals | Manage closely, first |
| Engineering Lead | Feasibility, delivery | High | High | Unambiguous requirements; awareness of technical constraints | Manage closely, early (As-Is discovery) |
| Finance | Approves refund/payment rules | Medium-High | High | Accurate reconciliation, refund audit trail, no duplicate/leaked payments | Manage closely, early |
| Operations Manager | Process owner | Medium | High | Defined cancellation/return handling; exception visibility | Manage closely, early |
| Warehouse Manager | Fulfilment/inventory owner | Medium | High | Accurate stock counts; cancellations reaching them before packing | Manage closely, early |
| Customer Support Lead | Voice of support pain | Low-Medium | High | Fewer "where is my order/refund" tickets; self-serve status | Keep satisfied, early |
| Support Agents | End users of admin tools | Low | High | Full order visibility; ability to act without escalation | Keep informed, early sample |
| Warehouse Staff | End users (pick/pack) | Low | High | Clear pick lists; no picking cancelled orders | Keep informed |
| Customer | Primary end user | Low (direct) | Very High | Clear status, easy cancellation, fast reliable refunds | Sample via tickets/survey |
| Payment Provider | External dependency | Low | Medium | Correct API usage, webhook/settlement handling | Consult (dependency) |
| Logistics Partner | External dependency | Low | Medium | Reliable shipment/delivery status data exchange | Consult (dependency) |
| QA Lead | Test owner | Low | Medium | Testable, unambiguous acceptance criteria | Keep informed |
| Legal/Compliance | Regulatory constraints | Medium | Low-Medium | Refund timelines, data/payment compliance | Consult as needed |

## Notes on Methodology
- Stakeholders were assessed on two separate axes: **Power** (ability to approve, block, or fund decisions) and **Impact** (how much the project outcome affects them) — rather than a single "influence" score, which conflates the two and leads to incorrect prioritisation.
- Early interviews were prioritised with stakeholders holding high impact and first-hand knowledge of the As-Is process (Support, Warehouse, Finance, Engineering), ahead of stakeholders who primarily validate direction (Product Manager, Business Owner were also engaged early for alignment, given their high power).
- Internal end-users with low formal power (Support Agents, Warehouse Staff) were still included, since system adoption depends on their buy-in, not just leadership approval.
