# Functional Requirements — OMS v2

## Purpose
Translate each Business Requirement (see `08-requirements/business-requirements.md`) into specific, testable system behaviors. FRs describe *what* the system must do when triggered by a specific event — not how it is technically implemented.

## FR-01 (from BR-01 — Payment-without-order detection)
| ID | Requirement |
|---|---|
| FR-01.1 | When a payment is confirmed successful by the gateway but the corresponding order-creation request fails or times out, the system shall log the transaction as an unresolved payment-order mismatch. |
| FR-01.2 | The system shall check for unresolved payment-order mismatches at a defined interval (e.g., every hour) and flag any mismatch older than the defined threshold. |
| FR-01.3 | When a mismatch is flagged, the system shall notify Finance through a defined channel within the 24-hour window. |

## FR-02 (from BR-02 — Status accuracy)
| ID | Requirement |
|---|---|
| FR-02.1 | When an order's actual status changes (e.g., picked, shipped, delivered), the system shall update the order's status field within 1 hour of that event occurring, and display the updated status along with a timestamp to the customer. |
| FR-02.2 | When an order's status changes, the system shall make the updated status (with timestamp) visible to internal teams — Support and Warehouse — within the same 1-hour window, through whatever internal system/dashboard they use to view orders. |

## FR-03 (from BR-03 — Cancellation exclusion from picking)
| ID | Requirement |
|---|---|
| FR-03.1 | When the picking list is generated, the system shall exclude any order whose status is "Cancelled" at that time, so cancelled orders never appear in the picking list in the first place. |
| FR-03.2 | When an order's status changes to "Cancelled" after it has already appeared on a generated picking list, the system shall flag that order as cancelled in a way that remains visible to warehouse staff until the flag is acknowledged or the order is pulled from dispatch — not a one-time alert that can be dismissed or missed. *[Exact notification timing/mechanism: TBD — design decision.]* |

## FR-04 (from BR-04 — Inventory restocking on return)
| ID | Requirement |
|---|---|
| FR-04.1 | When a warehouse staff member records a returned item's inspection result as "resellable," the system shall increment that item's stock count. |
| FR-04.1b | When a warehouse staff member records a returned item's inspection result as "damaged," the system shall exclude that item from sellable stock count. |
| FR-04.2 | The system shall maintain a separate "damaged stock" count, distinct from sellable inventory, so damaged-item volume remains visible for business reporting even though these items are excluded from sellable stock. |

## FR-05 (from BR-05 — Return reason capture and visibility)
| ID | Requirement |
|---|---|
| FR-05.1 | When a customer initiates a return request *(dependent on return-initiation mechanism — open gap, see business-requirements.md)*, the system shall require a return reason to be provided before the request is submitted. |
| FR-05.2 | When a customer has submitted a return reason, it shall be visible to both Warehouse and Finance. When Warehouse records an inspection result (per FR-04.1), that result shall also be visible to Finance alongside the customer's stated reason. |

## FR-06 (from BR-06 — Refund timing)
| ID | Requirement |
|---|---|
| FR-06.1 | When a refund is approved (whether from a return, a pre-shipment cancellation, or a resolved payment-order mismatch), the system shall initiate the refund process within 1 business day of that approval. |
| FR-06.2 | The system shall no longer batch refunds into fixed twice-weekly cycles; each approved refund shall be processed individually, as it is approved. |

## FR-07 (from BR-07 — Tracked cross-team coordination)
| ID | Requirement |
|---|---|
| FR-07.1 | When Support needs to query Warehouse about an order (e.g., status, cancellation handling), or when Warehouse needs to report a return to Finance, the system shall allow that query/report to be recorded against the specific order, visible to both parties, and retrievable later — rather than communicated through an untracked channel. |

## Notes on Methodology
- Each FR traces to exactly one parent BR, maintaining traceability from pain point → objective → business requirement → functional requirement.
- FRs intentionally avoid naming specific technical mechanisms (e.g., polling vs. webhook, specific UI pattern) where the underlying business need does not require one — these are left to technical design.
- Several items are marked `[TBD]` or flagged as dependent on an unresolved gap (e.g., FR-05.1's dependency on an undefined return-initiation mechanism) rather than guessed, consistent with the Assumptions log.
