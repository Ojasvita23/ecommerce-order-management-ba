# Assumptions & Constraints — OMS v2

## Purpose
Document beliefs taken as true but not yet verified (assumptions), and known limitations of the current system/organization that bound what Phase 1 can realistically deliver (constraints).

## Assumptions

| # | Assumption | Risk if Wrong | Verification |
|---|---|---|---|
| A1 | Refunds can always be issued to the original payment method | Some payment methods (expired cards, future COD) may not support this, requiring a fallback refund mechanism not yet designed | Confirm with Engineering / payment gateway documentation |
| A2 | Bank-side refund settlement takes 3-5 business days | O3's customer-facing commitment (refund initiated within 1 day) would be based on inaccurate downstream timing, undermining the objective's credibility | Confirm via payment gateway's settlement SLA/documentation |
| A3 | The order-creation API only fails via timeout | C2's auto-resolve logic may not catch other failure modes (e.g., validation errors, partial writes), leaving some payment-without-order cases unresolved even post-fix | Review API error handling with Engineering |
| A4 | Pre-shipment cancellations in the admin panel always succeed and immediately reflect in the system | If cancellation isn't reliably instant/successful today, C5 (excluding cancelled orders from picking) can't be built on top of it — the underlying cancellation action itself would need fixing first | Confirm with Engineering how cancellation writes are processed end-to-end |
| A5 | There is a consistent policy for handling post-shipment cancellation requests | If Support is making case-by-case judgment calls rather than following a real policy, business rules for this scenario don't exist yet and need to be defined from scratch | Confirm with Operations/Support Lead whether a written policy exists |
| A6 | The 6 PM cron job and daily warehouse CSV upload are the only two processes touching order status | If other integrations silently write to order status, C1's fix could be incomplete | Confirm with Engineering via a full audit of what writes to the order-status field |

## Constraints

| # | Constraint | Why It Limits Phase 1 |
|---|---|---|
| K1 | Team capacity: ~7 effective engineers (12 total, ~40% already committed elsewhere) | Caps how many of the 6 in-scope capabilities can realistically be delivered together |
| K2 | Timeline: 4-month delivery window | Forces prioritization if capacity and scope don't align |
| K3 | Order API has no webhook integration with the payment gateway today | C2 (detecting payment-without-order cases quickly) requires new integration work, not a small fix |
| K4 | Order status model is fixed at 5 values, written to only by two known processes (cron job, manual CSV upload) | Any added status granularity needed for C1/C5 requires schema and integration changes across existing touchpoints |
| K5 | No inventory increment logic exists anywhere in the current system | C4 must be built from scratch, including new business rules (pre-shipment vs. post-inspection restock), not just corrected |

## Notes on Methodology
- Assumptions are distinct from constraints: an assumption is an **unverified belief** (stated once by a stakeholder, not independently confirmed); a constraint is a **documented fact** about the current system or organization that bounds Phase 1's design regardless of preference.
- Each assumption includes a verification owner/method so it can be confirmed early, before it causes a costly late-stage redesign (the same logic behind splitting O4/O5 into baseline + target sub-objectives).
