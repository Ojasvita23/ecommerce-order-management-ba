# Scope & Out-of-Scope — OMS v2 Phase 1

## Purpose
Define what OMS v2 Phase 1 will and will not deliver, derived directly from the finalized business objectives (see `04-business-objectives/business-objectives.md`), and evaluated against team capacity and timeline constraints (7 effective engineers, 4-month window).

## In-Scope

| ID | Capability | Linked Objective |
|---|---|---|
| C1 | Reflect the true current order stage (Placed / Processing / Shipped / Delivered / Cancelled) without relying on a once-daily manual CSV upload | O1, O5b |
| C2 | Detect payment-succeeded-but-no-order cases within a short window and auto-resolve (complete order or trigger refund) | O2 |
| C3 | Trigger refund initiation immediately on approval, independent of the twice-weekly batch cycle | O3 |
| C4 | Restock inventory immediately on pre-shipment cancellation; restock returned items only after warehouse inspection confirms resellable condition | O4b |
| C5 | Ensure cancellation status is reflected before the daily picking list is generated, so cancelled orders are excluded from picking/dispatch | O5b |
| C6 | Give Support and Warehouse direct, traceable visibility into order/payment status, replacing informal WhatsApp coordination | O6 |

## Out-of-Scope

| Item | Reason |
|---|---|
| Exchanges | Does not serve any current objective (O1-O6); would add checkout/inventory/refund complexity unrelated to the problems this phase addresses |
| Dealer portal for bulk buyers | Structural mismatch — ShopKart operates D2C; a dealer/bulk-buyer portal represents a different business model |

## Deferred to Phase 2

| Item | Reason |
|---|---|
| Live courier map tracking | Depends on order status first becoming reliable (C1); also requires integrating an unanalyzed external logistics API — too large for current capacity |
| New mobile app | Does not directly address O1-O6; would compete for the same limited engineering capacity needed for core fixes |
| Cash on Delivery (COD) support | Introduces new business rules across cancellation and refund logic (e.g., no "original payment method" to refund cash to) that would add complexity to flows this phase is trying to stabilize first |

## Open Questions for Product Manager
1. For C2, when a payment succeeds but the order cannot be completed (e.g., stock ran out in that window), should the system attempt to complete the order anyway, or default to an automatic refund? This is a business policy decision, not purely a technical one.
2. Given 7 effective engineers across 6 capabilities in a 4-month window, if we cannot realistically deliver all six on time, would the business prefer to extend the delivery date, or deliver C1/C2/C3 in this phase and defer C4/C5/C6 to a follow-up phase?

## Notes on Methodology
- Every in-scope capability is traceable to a specific business objective — no capability was included solely because a stakeholder requested it.
- Items were classified as **Out-of-Scope** (will not be built as part of this project) versus **Deferred** (a reasonable future phase item, not rejected outright) based on whether they had any relevance to current objectives, and whether they depended on capabilities this phase must fix first.
