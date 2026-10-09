# Traceability Matrix: OMS v2 Phase 1

## Purpose
Shows that every requirement traces back to a real pain point and objective, and forward to the stories, use cases, rules, edge cases and APIs that deliver it. It is also a coverage check: anything that fails to trace in either direction is listed as a gap. The UAT column is filled in at the next stage.

## 1. Forward Traceability (by Business Requirement)

| BR | Pain points | Objective | FR | User stories | Use cases | NFR / Business rules | Edge cases | APIs | UAT |
|---|---|---|---|---|---|---|---|---|---|
| BR-01 Payment-without-order detection | P2 | O2 | FR-01.1, 01.2, 01.3 | US-01 | UC-01 | NFR-01, 02, 03, 07; BRule-01; Pending Rule 2 | EC-01, EC-04 | API-01, 02, 03, 04 | TBD |
| BR-02 Status accuracy | P1, P7 | O1 | FR-02.1, 02.2 | US-02, US-03 | UC-04 | NFR-01, 06 | EC-05 | API-05, 06 | TBD |
| BR-03 Cancellation exclusion and flagging | P5, P6, P6a, P7 | O5a, O5b | FR-03.1, 03.2 | US-04, US-05 | UC-02 | NFR-07; BRule-03 | EC-02, EC-03 | API-07, 08, 13 | TBD |
| BR-04 Inspection and restocking | P8 | O4a, O4b | FR-04.1, 04.1b, 04.2 | US-06 | UC-03 (main step 6-7, A1) | NFR-07; BRule-02, BRule-04; Pending Rule 1 | none | API-09 | TBD |
| BR-05 Return reason capture and visibility | P9, P9a | O4b | FR-05.1, 05.2 | US-07, US-08 | UC-03, UC-05 (A1) | NFR-05; BRule-05, BRule-06 | none | API-10, 12 | TBD |
| BR-06 Refund timing | P11 | O3 | FR-06.1, 06.2 | US-09 | UC-01 (6a), UC-03 (step 11) | NFR-02, 03, 07; BRule-01 | EC-04 | API-11 | TBD |
| BR-07 Tracked coordination | P3, P4, P10 | O6 | FR-07.1 (also FR-05.2) | US-10, US-11 | UC-05 | NFR-04, 05 | EC-06 | API-12 | TBD |

## 2. Backward Coverage: Pain Points

| Pain point | Covered by | Status |
|---|---|---|
| P1 | BR-02 | Covered |
| P2 | BR-01 | Covered |
| P3 | BR-07 | **Partial.** Payment-status visibility for Support has no FR (Gap 3) |
| P4 | BR-07 | Covered |
| P5 | BR-03 | Covered |
| P6 | BR-03 | Covered |
| P6a | BR-03 | Open, not solved in Phase 1 (IR-13) |
| P7 | BR-02, BR-03 | Covered |
| P8 | BR-04 | Covered |
| P9 | BR-05 | Covered |
| P9a | BR-05 | Open, mechanism undecided (IR-14) |
| P10 | BR-07 | Covered |
| P11 | BR-06 | Covered |
| P12 | None | Intentional. Organizational, flagged only |
| P13 | None | **Gap 1.** Not traced to any BR |

## 3. Backward Coverage: Objectives and Scope

| Item | Covered by | Note |
|---|---|---|
| O1 | BR-02 | Target (tickets ≤20%) is verified after launch, not in UAT |
| O2 | BR-01 | |
| O3 | BR-06 | |
| O4a, O5a | BR-04, BR-03 | Baseline measurements, done as project activities, not system features |
| O4b | BR-04, BR-05 | |
| O5b | BR-03 | |
| O6 | BR-07 | |
| C1 | BR-02 | |
| C2 | BR-01 | Scope says "auto-resolve", BR-01 says human-driven (see BR-01 scope note) |
| C3 | BR-06 | |
| C4 | BR-04 | **Partial.** Pre-shipment cancellation restock is not covered (Gap 2) |
| C5 | BR-03 | |
| C6 | BR-07 | |

## 4. Gaps Found

| # | Gap | Where it shows | Proposed fix |
|---|---|---|---|
| 1 | **P13 (no post-shipment cancellation policy) traces to no BR.** BRule-03 states the rule but nothing links the pain point to it | Pain points vs BR table | Add P13 to BR-03's pain-point list, and keep the cutoff decision as an open item (BR open question 3) |
| 2 | **Scope item C4 is half-covered.** Restocking on pre-shipment cancellation has no BR, FR, story or use case. Only returns are covered | C4 vs BR-04 / FR-04 | Extend BR-04, add FR-04.3 (restock when an order is cancelled before shipment), plus a story. Note K5 and API-09 |
| 3 | **US-03's third criterion has no parent FR.** It lets Support see payment status, but FR-02.2 covers order status only (BR open question 4) | US-03 vs FR-02 | Add a payment-status visibility FR under BR-07, or remove the criterion |
| 4 | **Pre-shipment cancellation refunds have no use case.** FR-06.1 and the ERD list it as a refund source, but no UC defines approval and initiation | FR-06.1, ERD vs UC | Add a short UC, or extend UC-03's scope |
| 5 | **FR-06.1 vs BRule-01 mismatch** (system initiates vs Finance initiates) | Already noted in the data model | Align the two wording-wise |
| 6 | **UAT cannot prove O1, O4a, O5a.** They are measured outside testing | Section 3 | Record as post-launch measures in the final summary |

## Notes on Methodology
- IDs were followed in both directions: top-down (pain point to API) shows what each need produces, bottom-up (API to pain point) shows nothing was built without a reason.
- Every FR has at least one story, and every story has a parent FR or BR except where Gap 3 applies.
- Gaps are recorded, not silently patched, so the matrix shows what the check found.
