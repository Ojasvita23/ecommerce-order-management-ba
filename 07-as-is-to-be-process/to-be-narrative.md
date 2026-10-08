# To-Be Process Narratives: OMS v2 Phase 1

## Purpose
Plain-language description of how the four core order flows will work after OMS v2 Phase 1, based on BR-01 to BR-07, FR-01 to FR-07, the Business Rules, and UC-01 to UC-05. Decisions that are still open are stated as open, not resolved by assumption. See `to-be-process.md` for the diagram.

## Sub-Flow 1: Happy Path
A customer browses ShopKart's catalog and completes payment at checkout. If the payment succeeds, the system automatically creates the order and sets its status to "Placed." From here, the order moves through picking, packing, and dispatch, and at every stage the system updates its status within 1 hour of the actual event, rather than waiting for a once-daily batch. The customer and the internal teams (Support, Warehouse) see the updated status with a timestamp in that same window, so a customer checking their order sees where it actually stands, not a status frozen since the previous evening. The reliance on a single daily cron job and a manual CSV upload for status is removed.

## Sub-Flow 2: Payment-Without-Order
A customer completes checkout and payment succeeds, but the order-creation step fails. The system detects the mismatch directly and flags it if it stays unresolved beyond a defined threshold. Finance is notified within 24 hours, instead of discovering the case a week later through manual reconciliation or a customer complaint. Finance reviews the transaction details and resolves the case by either refunding the customer or arranging manual order creation. Which of the two should be the default is still an open business decision, so Finance continues to decide case by case, but now sees every case within 24 hours.

## Sub-Flow 3: Cancellation-After-Shipment
When a customer cancels an order, the cancellation is reflected in the system, and any order in "Cancelled" status is excluded when the picking list is generated. If a cancellation happens after an order is already on a generated picking list, the system raises a persistent flag for the warehouse team. It stays visible until the order is actually pulled from packing or dispatch, so it can't be missed the way a one-time alert can. One part remains unresolved: whether the courier is ever told about a cancellation after dispatch. If not, a cancelled order could still be delivered with no way to catch it. OMS v2 Phase 1 does not solve this, and it is carried forward as an open risk (P6a).

## Sub-Flow 4: Returns and Refunds
How a customer formally initiates a return is an unresolved gap that Phase 1 does not address. Once an item reaches the warehouse, it is inspected and the result (resellable or damaged) is recorded in the system. Resellable items are restocked into sellable inventory. Damaged items are excluded from sellable stock and tracked in a separate damaged-stock count. The customer's return reason and the inspection result are shown to Finance automatically, replacing the manual Excel handoff. Two decisions remain open: what happens to damaged items physically, and whether a refund is still issued when the damage may have been caused by the customer. Once a refund is approved, it is initiated within 1 business day and processed individually, not held for a twice-weekly batch. The bank then takes 3-5 business days to settle, which ShopKart does not control.

## Open Items Carried Forward
- Return-initiation mechanism is undefined.
- Courier awareness of cancellations after dispatch is unconfirmed.
- Refund eligibility depends on damage cause, and no policy or inspection process exists.
- Disposal of damaged items is undecided.
- Default resolution for payment-without-order (refund vs. manual order) is undecided.
