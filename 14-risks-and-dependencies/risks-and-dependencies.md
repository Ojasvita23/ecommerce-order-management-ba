# Risks and Dependencies: OMS v2 Phase 1

## Purpose
Project-level register of what could stop OMS v2 Phase 1 from meeting its objectives, and what it depends on. Technical integration risks are in `11-data-and-api-analysis/integration-risks.md` (IR-01 to IR-17) and are summarised here, not repeated.

## Rating scale
- **Likelihood:** High / Medium / Low, from current evidence
- **Impact:** High = an objective is missed or money/customers are harmed. Medium = delay or workaround. Low = inconvenience
- **Score:** High-High = Critical. Any High with a Medium = High. Everything else = Moderate or Low.

## 1. Project Risks

| ID | Risk | Category | Likelihood | Impact | Evidence | Response | Owner |
|---|---|---|---|---|---|---|---|
| R-01 | **Capacity shortfall.** Six capabilities with about 7 effective engineers cannot all be delivered in 4 months | Delivery | High | High | K1, K2, Scope open question 2 | Get the PM's decision now: extend the date, or deliver C1/C2/C3 first and defer C4/C5/C6. Re-estimate once Engineering confirms the integration unknowns | Product Manager, Engineering Lead |
| R-02 | **Key decisions arrive late** and block design (mismatch default rule, return initiation, damaged-item policy, cancellation cutoff) | Governance | High | High | Pending Rules 1 and 2, P9a, BR open question 3 | Hold one decision meeting early. Record owner and due date for each decision. Escalate undecided items to the Business Owner | Product Manager |
| R-03 | **Gateway or courier cannot support what the design assumes** (webhooks, events, idempotency) | Technical / External | Medium | High | IR-01, IR-03, IR-07 | Run a feasibility check with both providers before design is signed off. Prepare the fallback designs described in the integration risks | Engineering Lead |
| R-04 | **Objectives cannot be proven** because baselines are missing (inventory accuracy, cancelled-but-shipped orders) and the ~40% ticket figure is unverified | Measurement | High | Medium | O4a, O5a, O1 baseline note | Complete the cycle count and 2-week dispatch log review in the first 2 weeks. Verify ticket share through ticket tagging | Operations Manager, Support Lead |
| R-05 | **Stock-count change is built on unreliable data.** Restock logic is new (K5) and shelf stock already disagrees with system stock | Data | Medium | High | K5, P8, Raghav's interview | Cycle count before go-live. Restock only after inspection. Reconcile stock at launch | Warehouse Manager |
| R-06 | **Adoption fails.** Warehouse staff are measured on dispatch time and Support on tickets closed, and neither is measured on using the new flags and query log | Change | Medium | High | P12, interviews, O6 | Brief Support and Warehouse early, use them in UAT, and raise incentive alignment with the Business Owner. P12 itself is outside Phase 1 | Operations Manager, Support Lead |
| R-07 | **Scope creep.** Requests for exchanges, COD, live tracking or the mobile app enter Phase 1 | Scope | Medium | Medium | Out-of-scope and Phase 2 lists | Use the scope document as the baseline. New requests go through the PM with an impact assessment | Product Manager |
| R-08 | **Late discovery of a hidden writer to order status** breaks the real-time update design | Technical | Low-Medium | High | A6, IR-08 | Audit everything that writes to status before build starts | Engineering Lead |
| R-09 | **Assumptions turn out wrong** (A1 to A6), forcing redesign | Planning | Medium | Medium | Assumptions log | Verify A1 to A6 in the first sprint. Each has a named method | Engineering Lead, Finance |
| R-10 | **Refund policy and compliance not checked.** The 1-business-day target and refund timelines could conflict with payment or consumer regulations | Compliance | Low | Medium | Stakeholder analysis (Legal/Compliance) | Consult Legal early on refund timeline and payment-data handling | Business Owner, Legal |
| R-11 | **Business user availability.** Finance, Warehouse and Support cannot spare time for requirements review and UAT | Resourcing | Medium | Medium | Warehouse team already busy (interview) | Schedule UAT slots in advance. Keep sessions short and scenario-based | Operations Manager |
| R-12 | **Open gaps ship as known limitations** and customers still hit them (courier not informed of cancellations, no defined return initiation) | Residual | High | Medium | P6a, P9a, EC-02 | State them as known limitations in the release sign-off. Plan Phase 2 items. Keep the persistent cancellation flag as the main control | Product Manager |

## 2. Integration Risks (summary)
17 technical risks are detailed in `integration-risks.md`. The ones that decide whether Phase 1 can be built as designed:

| Risk | Summary |
|---|---|
| IR-01 | Gateway webhook support unconfirmed |
| IR-03, IR-04 | No idempotency on order creation and refunds |
| IR-07 | Courier event capability unconfirmed |
| IR-13 | Courier unaware of post-dispatch cancellation |
| IR-14 | Return initiation undefined |

## 3. Dependencies

| ID | Dependency | Type | Needed by | Risk if late or missing | Owner |
|---|---|---|---|---|---|
| D-01 | Payment gateway: webhooks, signed events, refund API, idempotency | External | Before API-03 / API-11 design | Core fix for P2 cannot be built (R-03) | Engineering Lead, Payment Provider |
| D-02 | Logistics Partner: delivery events and cancellation awareness | External | Before API-05 design | 1-hour status target cannot be guaranteed | Engineering Lead, Logistics Partner |
| D-03 | PM decision on mismatch resolution default (refund vs order) | Decision | Before FR-01 build | UC-01 stays ad hoc | Product Manager |
| D-04 | Decision on return-initiation mechanism | Decision | Before return flow design | API-10 and US-07 stay blocked | Product Manager, Operations |
| D-05 | Decision on damaged-item refund eligibility and disposal | Decision | Before FR-04 / UC-03 A1 build | Damaged returns have no defined outcome | Operations, Finance |
| D-06 | Decision on cancellation cutoff (shipment vs picking-list generation) | Decision | Before FR-03 build | BRule-03 cannot be enforced consistently | Business Owner, Operations |
| D-07 | Warehouse cycle count and dispatch log review (baselines) | Data | Within 2 weeks of start | Objectives cannot be measured (R-04) | Warehouse Manager |
| D-08 | Engineering audit: order-status writers, cancellation reliability, API failure modes | Internal | First sprint | Assumptions A3, A4, A6 stay unverified | Engineering Lead |
| D-09 | Finance notification channel chosen | Decision | Before FR-01.3 build | Notification cannot be implemented | Product Manager, Finance |
| D-10 | Engineering capacity released (about 40% already committed elsewhere) | Resource | Project start | Delivery date slips (R-01) | Business Owner |

## 4. Priority View

**Critical, act now:** R-01 (capacity), R-02 (decisions), R-03 (provider feasibility)
**High:** R-04, R-05, R-06, R-12
**Moderate:** R-07, R-08, R-09, R-11
**Low:** R-10

**First two weeks:** run the feasibility check with the gateway and courier (D-01, D-02), hold the decision meeting (D-03 to D-06, D-09), start baselines (D-07) and the engineering audit (D-08).

## 5. Assumptions linked to risks
A1 (refund to original method) leads to IR-06. A3 (order API fails only by timeout) leads to R-09 and IR-02. A4 (cancellation is instant) leads to IR-10. A6 (only two status writers) leads to R-08. Treat any one found false as a risk event, not a surprise.

## Notes on Methodology
- Risks were drawn from constraints, assumptions, open decisions and interview evidence, not generic project templates.
- Ratings are judgement calls on interview evidence. Where data is missing, the response is to measure first.
- Every dependency names an owner and a "needed by" point, so the register can be managed, not just read.
- Residual gaps (R-12) are accepted risks with an explicit sign-off, not hidden ones.
