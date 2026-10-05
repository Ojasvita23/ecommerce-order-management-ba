# Business Requirements — OMS v2

## Purpose
Translate consolidated pain points (see `07-as-is-to-be-process/pain-points-summary.md`) into business-language requirements — technology-agnostic statements of what the business needs, traceable to specific objectives and pain points.

## Requirements

| ID | Requirement | Pain Point(s) | Objective |
|---|---|---|---|
| BR-01 | The business requires the ability to detect, within 24 hours, any case where a customer's payment has succeeded but no corresponding order has been created in the system. Upon detection, Finance must be notified so they can manually resolve the case — either by processing a refund or by initiating manual order creation — within that same 24-hour window. *Scope note: resolution remains human-driven, pending a PM decision on auto-resolution policy. Support's payment-status visibility need is a separate, related requirement (see BR-07).* | P2 | O2 |
| BR-02 | The business requires order status to reflect the true current stage of the order (e.g., Picked, Shipped, Delivered) within 1 hour of that status actually changing — rather than the current once-daily update cycle — so that customers see accurate, trustworthy status and are not driven to contact Support simply because the system appears stuck. | P1, P7 | O1 |
| BR-03 | The business requires that an order never be included in the picking/dispatch process once its status is "Cancelled," regardless of when the cancellation occurs relative to list generation. Any order cancelled after the picking list has already been generated must be flagged to warehouse staff in a persistent, visible way — not a one-time alert — so packed-but-cancelled orders can be identified and pulled before dispatch, without requiring staff to manually re-check the entire list. | P5, P6, P6a, P7 | O5a/O5b |
| BR-04 | The business requires that returned items be inspected upon arrival, with resellable items restocked into inventory and damaged items excluded from sellable stock — so that system inventory counts accurately reflect what can actually be sold. | P8 | O4a/O4b |
| BR-05 | The business requires that a return reason be captured from the customer at the time of return request, and that this reason, along with the warehouse's inspection findings, be visible to Finance — so that refund decisions are informed rather than blind, and recurring return causes can be analyzed for business decisions. | P9, P9a | O4b |
| BR-06 | The business requires that refunds be initiated within 1 business day of approval, rather than waiting for the next scheduled batch cycle (currently twice weekly) — so that the ShopKart-controlled portion of refund timing is fast and consistent, independent of the separate bank settlement time (3-5 business days), which remains outside the business's control. | P11 | O3 |
| BR-07 | Coordination between Support and Warehouse (e.g., order status queries, cancellation issues) and between Warehouse and Finance (e.g., return reporting) should happen through a tracked channel, replacing the current informal methods (WhatsApp, manual Excel), so queries and hand-offs are traceable and do not get lost. | P3, P4, P10 | O6 |

## Open Questions / Gaps Carried Forward
1. What happens operationally to a damaged returned item (discard, send to manufacturer, write-off)? — Not addressed by BR-04; needs an Operations decision.
2. How does a customer formally initiate a return today? — Undocumented; needs verification or fresh definition in To-Be design.
3. Should cancellation be disallowed once an order enters the picking list, even if not yet physically picked (similar to a railway chart-preparation cutoff)? — Trades customer cancellation window for operational simplicity; needs a Business Owner/Operations decision, not a default system behavior.
4. Is Support's payment-status visibility (tied to BR-01's resolution) adequately covered by BR-07, or does it need its own requirement?

## Notes on Methodology
- Each BR is traceable to specific pain point IDs and a business objective, maintaining a clean line for the eventual Traceability Matrix.
- BRs describe *what* the business needs, not *how* it should be built — implementation mechanisms (e.g., specific notification method, ticketing tool) are deliberately left open for the Functional Requirements and technical design stages.
