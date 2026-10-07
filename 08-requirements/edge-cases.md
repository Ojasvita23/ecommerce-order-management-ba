# Edge Cases — OMS v2

## Purpose
Boundary, timing, concurrency, and duplicate-action scenarios identified across the use cases — distinct from Exception Flows (expected deviations already handled within each use case). These often reveal gaps not caught by normal-path requirements.

## Edge Cases

| ID | Scenario | Risk / Why It Matters | Related Use Case / Requirement |
|---|---|---|---|
| EC-01 | A customer makes two separate transactions in close succession — the first fails to create an order, the second succeeds normally. | When Finance reviews the payment-without-order mismatch, the system must match and resolve the *correct* failed transaction without confusing it with the customer's separate successful order. | UC-01, FR-01.1 |
| EC-02 | A cancelled order is shipped and delivered to the customer before the cancellation is caught (courier was never informed, or informed too late); the customer keeps the item. | This is the realized worst-case outcome of the gap first identified as P6a — no detection or recovery mechanism currently exists. Still an open, unresolved gap. | UC-02, P6a |
| EC-03 | An order is cancelled at the exact moment the picking list is being generated (a race condition). | Depending on precise timing, the cancellation-status check (FR-03.1) may or may not catch it before the list locks in. Exact behavior at this boundary needs definition at the technical design stage. | UC-02, NFR-07 |
| EC-04 | Two different actions result in a refund being approved twice for the same case — not necessarily from the same user session (e.g., two Finance team members independently reviewing the same backlog item, or a retried API call). | This is a data/state-level risk, not a UI click-timing issue — frontend safeguards like debouncing do not address it. The system must prevent a second refund from being issued once one has already been recorded for that case. | UC-01, UC-03, NFR-07 |
| EC-05 | An order's status changes right at the edge of the 1-hour freshness window (FR-02.1). If updates are driven by a scheduled job rather than triggered immediately by the event, some changes could take close to — or technically over — 1 hour depending on exactly when in the cycle they occur. | FR-02.1 needs to be unambiguous about whether "within 1 hour" is a strict guarantee for every case or an acceptable average. The specific mechanism used to guarantee it (event-driven vs. frequent polling) is a technical design decision, not something the requirement should prescribe. | UC-04, FR-02.1 |
| EC-06 | A Support team member records a query against an order, but the Warehouse team member who could answer it is unavailable (on leave, shift ended) for an extended period. | No interview addressed whether an unanswered query should escalate after a certain time, or sit unresolved indefinitely. A new gap, similar in spirit to UC-01's unresolved-mismatch exception. | UC-05, BR-07 |

## Notes on Methodology
- Edge cases were identified systematically by applying four lenses to each use case: timing/concurrency, boundary values, partial/incomplete data, and repeat/duplicate actions.
- Each edge case ties back to a specific use case and, where applicable, the NFR or requirement it stresses — several (EC-03, EC-04) directly validate the need for NFR-07 (data integrity).
- Where resolution requires a technical implementation choice (EC-05), the edge case documents the ambiguity and the need for clarification, without prescribing the specific mechanism — consistent with the project's "what, not how" discipline throughout.
