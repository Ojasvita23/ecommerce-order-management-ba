# Business Rules — OMS v2

## Purpose
Define atomic, testable statements of business policy — constraints and conditions that hold true regardless of system implementation. These complement Functional Requirements (which describe system behavior) and Non-Functional Requirements (which describe system qualities).

## Confirmed Rules

| ID | Rule | Source |
|---|---|---|
| BRule-01 | Only users with the Finance role may initiate a refund. | NFR-05 |
| BRule-02 | Only users with the Warehouse role may update inventory stock status (resellable/damaged) or stock counts. | NFR-05 |
| BRule-03 | Cancellation is permitted only before an order has shipped; once shipped, cancellation is not allowed. *Note: current operational enforcement is unreliable (see P5/P6/P6a). Whether the enforceable cutoff should instead be "picking list generation" rather than literal shipment is an open question for Business Owner/Operations — see Scope document's open questions.* | Priya (Support Lead) |
| BRule-04 | A returned item identified as damaged during inspection must not be counted as sellable stock. | BR-04 |
| BRule-05 | A return cannot be processed by Finance until a return reason has been captured from the customer. | BR-05 |
| BRule-06 | A submitted return reason cannot be edited or deleted by any viewer (Warehouse, Finance, Support) once submitted. | US-08 |

## Pending Business Rules (Awaiting Decision)

| # | Open Rule | Dependency |
|---|---|---|
| 1 | Damaged return disposition: what should happen to a returned item once identified as damaged (discard, return to manufacturer, write-off)? No policy currently exists. | Ties to BR-04's open question |
| 2 | Payment-without-order resolution: when a payment succeeds but no order is created, should Finance default to refunding the customer or creating the order manually? Currently decided ad hoc, by whichever action happens first — no defined rule. | Ties to BR-01's scope note and PM Question #1 (see Scope document) |

## Notes on Methodology
- Business Rules are kept separate from Functional Requirements: a rule states the policy ("only Finance may initiate a refund"); a related FR states the system's enforcement of it.
- Rules without a confirmed, decided policy are explicitly listed as pending rather than guessed — consistent with the project's Assumptions & Constraints log.
