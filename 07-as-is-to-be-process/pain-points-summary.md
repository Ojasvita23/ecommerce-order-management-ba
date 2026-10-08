# Pain Points Summary: OMS v2

## Purpose
Consolidates the pain points found through stakeholder interviews (Support, Warehouse, Finance, Engineering) and the As-Is process review into one table, with the business impact of each and the objective it maps to. This is the bridge between As-Is analysis and the Business Requirements.

## Pain Points

| ID | Pain Point | Business Impact | Linked Objective |
|---|---|---|---|
| P1 | Order status doesn't reflect real progress; "Processing" can sit for up to ~24 hours because of the 6 PM batch cron | Drives ~40% of support tickets; erodes customer trust | O1 |
| P2 | ~60 cases per month where payment succeeds but no order is created, caused by a silent order-API failure with no retry or webhook listener | Direct revenue and trust risk: customers are charged with nothing to show for it, for up to a week | O2 |
| P3 | Support has no visibility into payment status | Forces manual Slack pings to Finance; slows ticket resolution | O2, O6 |
| P4 | Support-Warehouse coordination happens over untracked WhatsApp | No audit trail; slow, inconsistent responses | O6 |
| P5 | Warehouse learns of cancellations only when it opens the daily picking list | Cancellations are missed if they occur after the 6 PM cutoff | O5a, O5b |
| P6 | Cancelled orders sometimes get packed or shipped anyway | Wasted shipping cost, customer confusion, returns-handling overhead | O5a, O5b |
| P6a | Whether the courier is ever told about a cancellation is unconfirmed; if not, the item is delivered normally with no way to flag or recover it | Silent, unrecoverable inventory and revenue leak, potentially worse than P6 because nothing self-corrects | O5b, O4b |
| P7 | Picking list is generated once daily via cron, reading all "Placed" orders | Root structural cause behind P1 and P5/P6 delays | O1, O5b |
| P8 | Returned and damaged items never restock inventory; no increment logic exists anywhere in the system | Core driver of inventory mismatch; warehouse stock figures are unreliable | O4a, O4b |
| P9 | Returns arrive with no advance notice or reason captured | Finance refunds blind, with no visibility into cause or product condition | O4b, O3 |
| P9a | How a customer formally initiates a return is undocumented; no confirmed process exists | A full stage of the order lifecycle has no defined process to build on | O4b |
| P10 | Returns reported to Finance via manual Excel sheet | Untraceable, error-prone, no system record | O4b, O6 |
| P11 | No defined refund timeline; customers told inconsistent estimates (2-10 days in practice) | Damages trust; refunds batched only twice weekly, adding avoidable delay | O3 |
| P12 | Support is measured on ticket-closure speed, not resolution quality | Organizational incentive misaligned with real fixes; flagged only, not solvable by OMS v2 directly | (flag only) |
| P13 | No defined policy for post-shipment cancellation requests; handled by individual escalation and judgment | Inconsistent customer experience; no documented business rule to implement | O5b |

## Notes on Methodology
- P6a and P9a were not reported by any stakeholder. They were found by stress-testing the As-Is flows and are labelled as gaps, not confirmed facts.
- Pain points are written as problems, not solutions, and each is separated from its symptoms where possible (for example, P7 is a root cause of P1 and P5/P6).
- P12 is recorded for completeness but is organizational, so no requirement in this project addresses it.
- Each pain point maps to at least one objective in `04-business-objectives/business-objectives.md`, which keeps the chain from pain point to objective to requirement traceable.
