# UAT Scenarios: OMS v2 Phase 1

## Purpose
Business-level acceptance scenarios, run by the people who will use the system (Finance, Warehouse, Support, and a customer proxy), to confirm that each requirement works as the business intended. Scenarios are derived from the acceptance criteria in `user-stories.md`, the use cases and the edge cases. Each traces back to the matrix.

## Conventions
- **Priority:** High = money, stock or customer harm if it fails. Medium = efficiency or visibility.
- **Result:** Pass / Fail / Blocked. Any High-priority Fail blocks release.
- Scenarios that depend on an undecided gap are listed separately at the end, not guessed.

## Scenarios

| ID | Scenario | Tester | Given / When / Then | Traces to | Priority |
|---|---|---|---|---|---|
| UAT-01 | Payment taken, no order created | Finance | **Given** a payment succeeded but order creation failed. **When** the threshold passes without resolution. **Then** the mismatch is flagged and Finance is notified within 24 hours with amount, customer and timestamp visible | US-01, UC-01, FR-01.1 to 01.3 | High |
| UAT-02 | Resolve mismatch by refund | Finance | **Given** a flagged mismatch. **When** Finance chooses refund and approves. **Then** the refund is initiated within 1 business day and the mismatch is marked resolved and logged | UC-01 (6a), FR-06.1, NFR-03 | High |
| UAT-03 | Resolve mismatch by manual order | Finance, Support | **Given** a flagged mismatch. **When** Finance requests manual order creation. **Then** the order is created, enters as Placed, and follows the normal flow | UC-01 (6b) | High |
| UAT-04 | Two transactions in quick succession | Finance | **Given** a customer's first transaction failed and the second succeeded. **When** the mismatch is reviewed. **Then** only the failed transaction is flagged and the successful order is untouched | EC-01 | High |
| UAT-05 | Customer sees current status | Customer proxy | **Given** an order's status changes (e.g. Shipped). **When** the customer views the order. **Then** the new status and its timestamp appear within 1 hour | US-02, UC-04, FR-02.1 | High |
| UAT-06 | Internal teams see the same status | Support, Warehouse | **Given** the same status change. **When** an internal user views the order. **Then** the status and timestamp match, with no need to contact another team | US-03, FR-02.2 | Medium |
| UAT-07 | Cancelled order excluded from picking list | Warehouse | **Given** one Cancelled and one Placed order before list generation. **When** the list is generated. **Then** only the Placed order appears | US-04, FR-03.1 | High |
| UAT-08 | Late cancellation is flagged and stays flagged | Warehouse | **Given** an order already on the list. **When** it is cancelled. **Then** it shows a visible cancelled flag that does not disappear until the order is pulled from packing or dispatch | US-05, FR-03.2, UC-02 (A1) | High |
| UAT-09 | Cancellation at the moment of list generation | Warehouse | **Given** an order cancelled while the list is being generated. **When** the list is produced. **Then** the order is either excluded or flagged, and never silently picked | EC-03 | High |
| UAT-10 | Resellable return restocks | Warehouse | **Given** a returned item. **When** it is inspected and marked Resellable. **Then** sellable stock rises by one, once only | US-06, FR-04.1, NFR-07 | High |
| UAT-11 | Damaged return is not sellable | Warehouse | **Given** a returned item. **When** it is marked Damaged. **Then** sellable stock is unchanged and the damaged count rises | US-06, FR-04.1b, FR-04.2, BRule-04 | High |
| UAT-12 | Return reason visible and protected | Warehouse, Finance, Support | **Given** a submitted return reason. **When** each role views it. **Then** all three can see it, none can edit or delete it, and other roles are denied | US-08, BRule-06 | Medium |
| UAT-13 | Inspection result reaches Finance automatically | Warehouse, Finance | **Given** an inspected return. **When** Finance opens the record. **Then** the result, customer reason and order details are in one place with no Excel sheet | US-11, FR-05.2 | High |
| UAT-14 | Refund is initiated individually and quickly | Finance | **Given** an approved refund. **When** approval is recorded. **Then** the refund is initiated within 1 business day, not held for the next batch | US-09, FR-06.1, FR-06.2 | High |
| UAT-15 | Duplicate refund is blocked | Finance (two users) | **Given** a refund already recorded for a case. **When** a second approval is attempted. **Then** the system prevents a second refund | EC-04, NFR-07 | High |
| UAT-16 | Only Finance can refund | Support, Warehouse, Finance | **Given** users of different roles. **When** each tries to initiate a refund. **Then** only Finance succeeds and the action is logged against the user | BRule-01, NFR-05 | High |
| UAT-17 | Support logs a query to Warehouse | Support, Warehouse | **Given** a question about an order. **When** Support records it and Warehouse answers. **Then** both see it on the order, with timestamps and names, and it stays retrievable | US-10, FR-07.1, NFR-04 | Medium |
| UAT-18 | Notification failure is not silent | Finance | **Given** a Finance notification or refund call that fails. **When** the failure occurs. **Then** it is retried and every attempt is logged | NFR-02, NFR-03 | Medium |

## Not testable yet (blocked by open decisions)

| Item | Why blocked | Needed before |
|---|---|---|
| Return request with mandatory reason (US-07) | Return-initiation mechanism is undefined (P9a) | A decision from Product/Operations |
| Refund for a damaged return | Damage-cause policy undecided (Pending Rule 1) | Business rule decision |
| Disposal of damaged items | Undecided | Operations decision |
| Courier told of a post-dispatch cancellation | Unconfirmed (P6a, EC-02) | Logistics Partner confirmation |
| Escalation of unanswered queries | No rule defined (EC-06) | Operations decision |
| Default mismatch resolution (refund vs order) | Undecided (Pending Rule 2) | PM decision |

## Not proven by UAT
O1 (ticket share falls to ≤20%), O4a and O5a (baselines) are measured after launch or as project activities. They are tracked in the final summary, not here.

## Exit criteria
- All High scenarios pass.
- No open High-severity defects.
- Each Medium failure has an agreed workaround or fix date.
- Blocked items are listed as known limitations in the release sign-off.

## Notes on Methodology
- Scenarios come from acceptance criteria and edge cases, so every one traces to an existing ID.
- UAT checks business outcomes. Technical checks such as retries and concurrency are the QA Lead's, and are referenced here only where the business can observe the result.
- Blocked scenarios are shown explicitly instead of being written against assumptions.
