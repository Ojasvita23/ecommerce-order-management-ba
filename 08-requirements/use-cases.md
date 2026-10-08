# Use Cases — OMS v2

## Purpose
Detailed, step-by-step interactions between actors and the system for each major capability, including main flows, alternate flows, and exceptions. Builds on Business Requirements, Functional Requirements, and User Stories.

---

## UC-01: Resolve Payment-Without-Order Mismatch
**Actor:** Finance team member, System
**Preconditions:** A payment has succeeded via the gateway, but order creation failed.

**Main Flow:**
1. System detects the payment-order mismatch (FR-01.1).
2. System flags the mismatch if unresolved beyond the defined threshold (FR-01.2).
3. System notifies Finance within 24 hours (FR-01.3).
4. Finance reviews transaction details (amount, customer, timestamp).
5. Finance decides the resolution path: refund or manual order creation *(currently ad hoc — pending Business Rule decision)*.
6a. **If refund:** Finance initiates refund (within 1 business day, per BR-06/US-09).
6b. **If manual order creation:** Finance requests Support to create the order; order proceeds through the normal happy-path flow from "Placed" onward.

**Exception Flow:**
- E1: If Finance takes no action within a defined SLA, the case remains open and escalates *(mechanism TBD)*.

**Postconditions:** Customer either receives a refund or has a valid order; the mismatch is marked resolved and logged (NFR-03).

---

## UC-02: Exclude and Flag Cancelled Orders
**Actor:** Warehouse team member, System
**Preconditions:** Orders exist in "Placed" status; the daily picking list is about to be generated.

**Main Flow:**
1. System generates the picking list, including only orders whose status is "Placed" at that moment (FR-03.1).
2. System excludes any order whose status is "Cancelled" at generation time — these orders never appear on the list.
3. Warehouse team member picks and packs the orders on the list.
4. Orders proceed to dispatch.

**Alternate Flow (A1 — Cancellation after list generation):**
1. An order already on the generated picking list is cancelled.
2. System flags that order as cancelled, visibly and persistently, to the warehouse team member (FR-03.2).
3. The flag remains visible until the order is physically pulled from packing/dispatch — not merely acknowledged (US-05).
4. Warehouse team member locates and removes the flagged order from packing/dispatch.
5. Once removed, the flag is cleared.

**Exception Flow:**
- E1: If the flagged order is missed and proceeds to dispatch anyway, it falls into a separate, still-partially-unresolved scenario (courier cancellation-awareness gap, P6a).

**Postconditions:**
- Main Flow: No cancelled order ever appears on the picking list or reaches packing.
- Alternate Flow: A late-cancelled order is identified and removed before dispatch.
- Exception: If missed, the order is dispatched despite cancellation (P6a, unresolved).

---

## UC-03: Process a Return and Refund
**Actor:** Customer, Warehouse team member, Finance team member, System
**Preconditions:** Customer has a delivered order eligible for return. *(Exact return-initiation mechanism remains an open gap — not confirmed by any interview; assumed here as a system-provided method, TBD.)*

**Main Flow:**
1. Customer initiates a return request *(mechanism TBD)*.
2. System requires the customer to provide a return reason before the request can be submitted (FR-05.1).
3. Customer submits the return request with reason attached.
4. Item is transported back to the warehouse via courier (transport only — no courier-level inspection).
5. Warehouse team member inspects the item upon arrival.
6. Warehouse team member records the inspection result as "resellable" (FR-04.1).
7. System increments the sellable inventory count for that item.
8. System makes the inspection result and customer's return reason visible to Finance (FR-05.2, US-11).
9. Finance reviews the return reason and inspection result.
10. Finance approves the refund.
11. System initiates the refund within 1 business day of approval, processed individually — not batched (FR-06.1, FR-06.2).
12. Refund moves to the bank/payment provider, which settles it in 3-5 business days (outside ShopKart's control).

**Alternate Flow (A1 — Item found damaged):**
1. Warehouse team member records the inspection result as "damaged" (FR-04.1b).
2. System excludes the item from sellable inventory and records it in a separate damaged-stock count (FR-04.2).
3. Whether a refund proceeds depends on the cause of damage — defect/transit issue (refund likely proceeds) vs. customer misuse (refund may be reduced, denied, or require review). No policy or inspection mechanism for determining/recording this cause currently exists — unresolved pending business rule, not a default behavior.
4. What happens to the physical damaged item afterward (discard, return to manufacturer, write-off) also remains an open business decision.

**Postconditions:**
- Main Flow: Item is restocked as sellable inventory; customer's refund is initiated within 1 business day and later settled by the bank; return reason and inspection result are recorded and visible to Warehouse, Finance, and Support.
- Alternate Flow: Item is excluded from sellable stock and tracked separately as damaged; whether the customer receives a refund is undetermined pending a damage-cause policy decision; physical disposition of the item also remains unresolved.

---

## UC-04: View Order Status
**Actor:** Customer, Support team member, Warehouse team member, System
**Preconditions:** An order exists and has a status.

**Main Flow:**
1. An order's actual status changes (e.g., picked, shipped, delivered).
2. System updates the order's status field within 1 hour of that event (FR-02.1).
3. Customer views their order and sees the updated status along with the timestamp of the last change.
4. Separately, Support or Warehouse team member views the order internally and sees the same updated status and timestamp, within the same 1-hour window (FR-02.2).

**Postconditions:** Customer and internal teams both see an accurate, current status with timestamp, without needing to contact another team to confirm it.

---

## UC-05: Cross-Team Query and Coordination
**Actor:** Support team member, Warehouse team member, Finance team member, System
**Preconditions:** An order exists that requires cross-team input (e.g., a status question, a cancellation issue, or a return needing refund processing).

**Main Flow (Support ↔ Warehouse):**
1. Support team member has a question about a specific order (e.g., cancellation status).
2. System allows Support to record the query against that order, visible to Warehouse.
3. Warehouse team member views and responds to the query.
4. The query and its resolution remain recorded and retrievable against that order (FR-07.1, NFR-04).

**Alternate Flow (A1 — Warehouse → Finance, return reporting):**
1. Warehouse team member inspects a returned item.
2. System automatically makes the inspection result and return reason visible to Finance against that order — this direction is automatic, not query-based (US-11).
3. Finance reviews the information to process the refund.

**Postconditions:**
- Main Flow: The Support↔Warehouse query and its resolution are timestamped, attributed to the people involved, and retrievable later.
- Alternate Flow: Finance has the information needed to process a refund without requesting it manually from Warehouse.

## Notes on Methodology
- Each use case traces to its originating Business Requirements, Functional Requirements, and User Stories.
- Open gaps surfaced during use case development (e.g., damage-cause-based refund eligibility) are logged as new pending Business Rules rather than resolved by assumption.
- Exception flows point to related use cases or gaps rather than attempting to resolve issues outside their own scope.
