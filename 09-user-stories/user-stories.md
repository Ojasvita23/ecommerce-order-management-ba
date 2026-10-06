# User Stories & Acceptance Criteria — OMS v2

## Purpose
Reframe Functional Requirements and Business Rules into role-centered stories, each with testable Given/When/Then acceptance criteria — the format Engineering/Agile teams track work against.

## US-01 (from FR-01 — Payment-without-order detection)
**As a** Finance team member, **I want** to be notified when a payment has succeeded but no order was created, **so that** I can resolve the case before it goes unnoticed for days.

**Acceptance Criteria:**
- Given a payment succeeds and order creation fails, when the mismatch remains unresolved beyond the defined threshold, then Finance receives a notification.
- Given a notification is received, Finance can view the transaction details (amount, customer, timestamp) needed to resolve it.
- The notification occurs within 24 hours of the mismatch occurring.

## US-02 (from FR-02.1 — Status accuracy, customer-facing)
**As a** customer, **I want** to see the updated status of my order, **so that** I know where it currently is.

**Acceptance Criteria:**
- Given an order's status changes, when I view my order, then the displayed status reflects that change within 1 hour of it occurring.
- Given the status is displayed, then it is shown along with the timestamp of when it last changed.

## US-03 (from FR-02.2 — Status accuracy, internal)
**As a** Support or Warehouse team member, **I want** to see an order's current status internally, **so that** I can answer customer queries or manage fulfillment without needing to contact another team.

**Acceptance Criteria:**
- Given an order's status changes, when I view that order internally, then the updated status is visible to me within 1 hour of the change.
- Given the internal status view, then it includes the timestamp of the last update.
- Given I am Support, when a customer asks about order or payment status, then I can retrieve this information without pinging Warehouse or Finance directly.

## US-04 (from FR-03.1 — Picking list exclusion)
**As a** warehouse team member, **I want** the picking list to contain only orders that are genuinely ready to be picked, **so that** I don't waste time picking and then having to unpack cancelled orders.

**Acceptance Criteria:**
- Given an order's status is "Cancelled" at the time the picking list is generated, when the list is generated, then that order does not appear on it.
- Given an order's status is "Placed" at the time the picking list is generated, when the list is generated, then that order does appear on it.

## US-05 (from FR-03.2 — Post-generation cancellation flagging)
**As a** warehouse team member, **I want** to be clearly and persistently alerted when an order on my picking list gets cancelled after the list was generated, **so that** I can pull it before it's packed or shipped.

**Acceptance Criteria:**
- Given an order on the picking list is cancelled after list generation, when I view my picking list or dashboard, then that order is visibly flagged as cancelled.
- Given an order is flagged as cancelled, then the flag remains visible until I acknowledge it or remove the order from packing/dispatch — it does not disappear on its own.
- *(Exact timing from cancellation to flag appearing: TBD per FR-03.2.)*

## US-06 (from FR-04.1/4.1b/4.2 — Inventory restocking)
**As a** warehouse team member, **I want** to record whether a returned item is resellable or damaged, **so that** the inventory count stays accurate and damaged items aren't treated as sellable stock.

**Acceptance Criteria:**
- Given a returned item is inspected, when I mark it as "resellable," then the sellable inventory count increases for that item.
- Given a returned item is inspected, when I mark it as "damaged," then the sellable inventory count does not increase, and the item is instead recorded in a separate damaged-stock count.
- *(What ultimately happens to damaged stock — discard, return to manufacturer, write-off — remains an open business decision; not addressed by this story.)*

## US-07 (from BR-05 — Return reason capture)
**As a** customer, **I want** to provide a reason when requesting a return `[dependent on return-initiation mechanism — open gap, see BR-05]`, **so that** the business understands why I'm returning the item.

**Acceptance Criteria:**
- Given I am initiating a return (mechanism TBD), when I submit the request, then I am required to provide a return reason before it can be submitted.
- *(Whether the customer can later view their own submitted reason is not addressed by this story.)*

## US-08 (from BR-05 — Return reason visibility)
**As a** Warehouse, Finance, or Support team member, **I want** to view the return reason a customer provided, **so that** I can understand why the product is being returned, use it for business analysis (Warehouse/Finance), or answer customer queries without escalating to another team (Support).

**Acceptance Criteria:**
- Given a return reason has been submitted, when I view the return record, then I can see the reason the customer provided — if I am Warehouse, Finance, or Support.
- Given I am viewing a return reason, then I cannot edit or delete it (BRule-06).
- Given I am not Warehouse, Finance, or Support, when I attempt to view a return reason, then access is denied.

## US-09 (from BR-06 — Refund timing)
**As a** Finance team member, **I want** approved refunds to be initiated as soon as they're approved, **so that** customers don't wait for the next scheduled batch cycle.

**Acceptance Criteria:**
- Given a refund is approved (from a return, pre-shipment cancellation, or resolved payment-order mismatch), when the approval is recorded, then the refund is initiated within 1 business day.
- Given a refund is approved, then it is processed individually — not held until the next twice-weekly batch cycle.
- Given a refund has been initiated, then bank settlement time (3-5 business days, outside ShopKart's control) is communicated separately and not counted against the 1-business-day target.

## US-10 (from BR-07 — Support↔Warehouse coordination)
**As a** Support team member, **I want** to log and track a query to Warehouse (e.g., about an order's cancellation or status), **so that** I have a record of what was asked and don't have to rely on WhatsApp messages that get lost.

**Acceptance Criteria:**
- Given I have a question about a specific order, when I raise it to Warehouse, then the query is recorded against that order and visible to both Support and Warehouse.
- Given a query has been raised, when it's resolved, then the resolution is also recorded and retrievable later.
- Given I check an order's history, then I can see any past queries and their resolutions tied to it.

## US-11 (from BR-07 — Warehouse→Finance returns reporting)
**As a** Warehouse team member, **I want** to report a return to Finance in a trackable way, **so that** Finance has what they need to process a refund without relying on a manually shared Excel sheet.

**Acceptance Criteria:**
- Given a returned item has been inspected, when I record the inspection result, then that result is automatically visible to Finance against that order — no separate Excel sheet is sent.
- Given a return has been recorded, when Finance reviews it, then they can see the inspection result, the customer's return reason, and the original order details together, in one place.
- Given a return record exists, then it remains retrievable later for audit purposes.

## Notes on Methodology
- Each story traces to a specific FR or BR; none introduce new scope beyond what was already defined and evidenced.
- Open gaps (e.g., return-initiation mechanism, damaged-stock disposition) are explicitly carried into acceptance criteria as unresolved, rather than silently assumed.
- Role scoping (e.g., who can view a return reason) is stated as a deliberate decision with reasoning, not a default.
