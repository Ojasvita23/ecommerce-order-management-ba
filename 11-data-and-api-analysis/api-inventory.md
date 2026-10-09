# API Inventory: OMS v2 Phase 1

## Purpose
Lists every API or integration point OMS v2 Phase 1 needs, traced to requirements. It states what each integration must do, not how it is built. Items that cannot be confirmed are marked Unconfirmed rather than assumed.

## Inventory

| ID | API / Integration | Type | Owner | Direction / Trigger | Traces to | Known unknowns |
|---|---|---|---|---|---|---|
| API-01 | Payment Initiation (gateway charge) | Existing | External (Payment Provider) | Checkout calls gateway to charge the customer | FR-01.1 | Does the gateway support an idempotency key so a retried charge is not duplicated? (EC-01) |
| API-02 | Order Creation | Changed | Internal | Called by checkout after payment success. Must return a clear success/failure outcome and accept the payment reference | FR-01.1, BR-01, P2 | Does it fail only by timeout? (A3). Can it safely be retried without creating duplicate orders? |
| API-03 | Payment Confirmation Webhook (gateway to OMS) | New | External to Internal | Gateway notifies OMS when a payment succeeds, independent of what the checkout call saw | FR-01.1, K3 | Does the gateway support webhooks? Signature verification, retry behaviour, out-of-order or duplicate delivery |
| API-04 | Mismatch Detection and Finance Notification | New | Internal | Scheduled check (e.g. hourly) compares successful payments with orders, flags aged mismatches, notifies Finance | FR-01.2, FR-01.3, UC-01, NFR-01, NFR-02, NFR-03 | Notification channel is undecided. Retry count is TBD |
| API-05 | Order Status Update | Changed | Internal (also fed by Logistics Partner events) | Called when a physical event occurs (picked, shipped, delivered). Updates status and timestamp within 1 hour | FR-02.1, FR-02.2, UC-04, EC-05, K4, A6 | Does the courier push events or must we poll? Do any other processes write to status? (A6). Event-driven vs frequent polling is a design decision |
| API-06 | Order Status Retrieval | Changed | Internal | Customer view and Support/Warehouse dashboard read status plus last-updated timestamp | FR-02.1, FR-02.2, US-02, US-03 | Does Support also need payment status in the same view? (P3, open question 4 in BRs) |
| API-07 | Order Cancellation | Changed | Internal | Support cancels in the admin panel. Sets status to Cancelled and triggers the post-generation flag if the order is already on a list | FR-03.1, FR-03.2, BRule-03, A4 | Is cancellation truly instant and reliable today? (A4). Cutoff rule (shipment vs picking-list generation) is an open business decision |
| API-08 | Picking List Generation and Flagging | Changed | Internal | Scheduled job builds the list excluding Cancelled orders. A later cancellation raises a persistent flag until the order is pulled | FR-03.1, FR-03.2, UC-02, EC-03, US-04, US-05 | Behaviour at the exact generation moment (race condition). Flag timing mechanism is TBD |
| API-09 | Record Inspection Result and Update Inventory | New | Internal | Warehouse records Resellable or Damaged. System increments sellable stock or increments damaged count, exactly once | FR-04.1, FR-04.1b, FR-04.2, BRule-02, BRule-04, NFR-07 | Must prevent double-increment for one return. Pre-shipment cancellation restock (C4) needs its own trigger |
| API-10 | Return Request Submission | New | Internal | Customer submits a return with a mandatory reason | FR-05.1, BRule-05, BRule-06, US-07 | **Unconfirmed.** Return-initiation mechanism is an open gap (P9a). Cannot be specified until decided |
| API-11 | Refund Initiation | Changed | Internal calling External (gateway refund API) | Finance approves, then refund is initiated individually within 1 business day to the original payment method | FR-06.1, FR-06.2, BRule-01, EC-04, NFR-02, NFR-03, NFR-07, A1, A2 | Duplicate-refund protection (idempotency). Fallback if the original method is invalid (A1). Who triggers: system or Finance (FR-06.1 vs BRule-01 mismatch) |
| API-12 | Cross-Team Query Logging | New | Internal | Support/Warehouse record and respond to queries against an order. Warehouse result visibility to Finance is automatic | FR-07.1, FR-05.2, NFR-04, US-10, US-11, EC-06 | Escalation of unanswered queries is undefined (EC-06) |
| API-13 | Courier Cancellation Notification | Unconfirmed | External (Logistics Partner) | Would tell the courier an order was cancelled after dispatch | P6a, EC-02, UC-02 (E1) | **Unconfirmed.** No interview confirms this exists. Out of Phase 1 scope, carried as an open risk |

## Summary
- Existing and changed: API-01, 02, 05, 06, 07, 08, 11. Phase 1 modifies these, so Engineering needs to confirm current behaviour.
- New: API-03, 04, 09, 10 (blocked), 12.
- External dependencies: Payment Provider (API-01, 03, 11) and Logistics Partner (API-05 events, API-13).
- Blocked by open gaps: API-10 (P9a) and API-13 (P6a). Both stay in the inventory as visible unknowns.

## Questions for Engineering
1. Does the payment gateway support webhooks and idempotency keys?
2. What exactly can cause the order API to fail besides timeout? (A3)
3. What else writes to the order-status field? (A6)
4. Does the courier provide delivery events by push or only by pull?

## Notes on Methodology
- Each API row was derived by walking every FR and UC and asking which two systems must exchange what data.
- Every row traces to existing requirement IDs so it fits the Traceability Matrix later.
- Rows describe required behaviour only. Mechanisms (polling vs events, notification tool) stay open for technical design.
