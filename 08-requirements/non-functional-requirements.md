# Non-Functional Requirements — OMS v2

## Purpose
Define system *qualities* — performance, reliability, auditability, security, scalability, and data integrity — required to properly support the Functional Requirements (see `08-requirements/functional-requirements.md`). Each NFR is tied to specific evidence from stakeholder interviews or the original problem statement, not generic boilerplate.

## Requirements

| ID | Category | Requirement | Justification |
|---|---|---|---|
| NFR-01 | Performance | The status-update and mismatch-detection jobs (FR-01.2, FR-02.1) shall complete within their defined windows even as order volume grows beyond the current ~15,000/month | So freshness targets (1-hour status, 1-hour mismatch detection) don't degrade as the business scales |
| NFR-02 | Reliability | If refund initiation or Finance notification fails, the system shall automatically retry rather than failing silently | BR-01's entire premise is fixing a silent-failure problem — a retry mechanism that itself fails silently would reproduce the exact issue the project exists to solve. *[Exact retry count: TBD — design decision.]* |
| NFR-03 | Reliability / Auditability | The system shall log every refund-initiation and notification attempt, success or failure | So failed cases can be manually identified and resolved rather than disappearing unnoticed — directly addresses the silent-failure pattern behind P2 |
| NFR-04 | Auditability | Every cross-team query or hand-off (FR-07.1) shall be timestamped and attributable to the person who raised and resolved it, and retrievable later | Gives coordination an audit trail, unlike today's WhatsApp/Excel methods, which leave no record |
| NFR-05 | Security | The system shall enforce role-based access control, such that sensitive actions (e.g., refund initiation, inventory status changes) are restricted to authorized roles and logged against the acting user's identity | Prevents unauthorized refund initiation or inventory changes; specific role-permission mappings (e.g., Finance-only refunds) are defined as Business Rules |
| NFR-06 | Scalability | The system shall support growth in order volume beyond the current ~15,000/month without degrading the performance targets defined in FR-01, FR-02, and FR-06 | The original business problem statement explicitly ties OMS v2 to scaling order volume without proportionally scaling support headcount — an architecture that only works at today's volume defeats that purpose |
| NFR-07 | Data Integrity | The system shall prevent duplicate or conflicting updates to the same order or inventory item when multiple actions occur concurrently (e.g., a refund triggered twice, stock incremented more than once for one return) | Raghav explicitly reported stock mismatches as an existing, unexplained problem today; new automated restocking (FR-04) and refund logic (FR-06) without this safeguard risks creating the same kind of silent inconsistency the project exists to eliminate |

## Notes on Methodology
- Each NFR is traced to specific evidence (an interview quote, a prior requirement, or the original problem statement) rather than written as generic system-quality boilerplate.
- Specific role-to-permission mappings (e.g., "only Finance can initiate a refund") are intentionally excluded from NFR-05 and instead captured as Business Rules, since they are discrete, testable rules rather than general system qualities.
- Unjustified numeric values (e.g., exact retry counts) are explicitly marked TBD rather than invented, consistent with the Assumptions & Constraints log.
