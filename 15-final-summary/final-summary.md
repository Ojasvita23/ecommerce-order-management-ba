# Final Summary: OMS v2 Phase 1 (ShopKart)

> A business analysis case study. ShopKart is a fictional D2C e-commerce company (~15,000 orders/month). All stakeholder input is simulated for the purpose of the case study.

## 1. The problem
ShopKart's order lifecycle depends on a daily batch job, manual file uploads and untracked messages. The result is customers who can't see where their order is, who get charged without receiving an order, and whose refunds take days. Internally, Support, Warehouse and Finance work from different information. The business wants to scale orders without scaling support cost in proportion.

## 2. Key findings
| Finding | Evidence |
|---|---|
| About 40% of support tickets are "where is my order", because status updates only once a day | P1, Support Lead interview (estimate, to be verified) |
| About 60 payments a month succeed with no order created. They are found by customer email or weekly manual reconciliation | P2, Finance and Engineering interviews |
| A cancelled order can still be picked, packed and shipped if it is cancelled after the 6 PM picking list is generated | P5, P6, Engineering and Warehouse interviews |
| Stock is never incremented on cancellation or return, and damaged items are counted as sellable | P8, K5, Engineering interview |
| Refunds go out in a twice-weekly batch, and Finance approves them without seeing the return reason | P9, P11 |
| Three problems were found by testing the As-Is flows, not reported by anyone: courier awareness of cancellations (P6a), return initiation (P9a), and what happens to damaged items | Analysis, labelled as gaps |

## 3. What Phase 1 delivers
Six capabilities, each traced to a measurable objective:

| Capability | Objective |
|---|---|
| C1 Status reflects reality within 1 hour | O1: tickets from ~40% to ≤20% |
| C2 Payment-without-order detected and flagged to Finance within 24 hours | O2: no case unresolved beyond 24 hours |
| C3 Refunds initiated within 1 business day of approval | O3: 95% within target |
| C4 Restocking after inspection (and on pre-shipment cancellation) | O4a/O4b: baseline, then improvement |
| C5 Cancelled orders excluded from picking, late cancellations flagged persistently | O5a/O5b: baseline, then near-zero shipped-after-cancel |
| C6 Tracked Support-Warehouse-Finance coordination | O6: 100% adoption |

Out of scope: exchanges, dealer portal. Deferred to Phase 2: live courier tracking, mobile app, Cash on Delivery.

## 4. Approach and deliverables
| Stage | Output | Folder |
|---|---|---|
| Discovery | Stakeholder analysis, interviews | 02 |
| Goals and boundaries | SMART objectives, scope, assumptions and constraints | 04 to 06 |
| Process | As-Is and To-Be diagrams and narratives, pain point summary | 07 |
| Requirements | 7 BRs, 15 FRs, 7 NFRs, 6 business rules, 5 use cases, 6 edge cases | 08 |
| Stories | 11 user stories with acceptance criteria | 09 |
| Data and API analysis | 15-entity logical ERD, 13-API inventory, specs for 4 key APIs, 17 integration risks | 11 |
| Validation | Traceability matrix, 18 UAT scenarios | 12, 13 |
| Delivery planning | 12 project risks, 10 dependencies | 14 |

## 5. Recommendations
1. **Resolve four decisions in the first two weeks.** Mismatch default (refund vs manual order), return-initiation mechanism, damaged-item policy, and the cancellation cutoff. Several requirements cannot be built or tested until they are made.
2. **Confirm provider capability before design.** The payment gateway (webhooks, idempotency, refund API) and courier (delivery events) decide whether the design holds as written.
3. **Fix the root causes first.** The once-daily batch (P7) sits behind P1, P5 and P6. C1, C2 and C3 deliver the most visible gains and should come first if capacity forces a split (7 effective engineers, 4 months).
4. **Measure before promising.** Take the inventory and cancelled-shipped baselines (O4a, O5a) in the first two weeks, and verify the ticket share.
5. **Build duplicate protection in from the start.** One refund per case and one order per payment, enforced in the data and API design.

## 6. Known limitations
- Courier awareness of post-dispatch cancellations is not solved in Phase 1 (P6a, IR-13).
- Return initiation, damaged-item refunds and disposal remain undecided (P9a, Pending Rule 1).
- P12 (Support measured on ticket speed, not quality) is organisational and outside OMS v2.
- O1, O4a and O5a are measured after launch or as project activities, and UAT cannot prove them.

## 7. What I would do next
Run the provider feasibility checks, hold the decision meeting, take the baselines, then re-plan scope and dates with real Engineering estimates. After that, Phase 2: courier tracking, mobile and COD.

## 8. Skills demonstrated
Stakeholder analysis, elicitation, process modelling, requirements at four levels (BR, FR, NFR, rules), user stories and acceptance criteria, data modelling, API analysis, edge-case discovery, traceability, UAT design, risk management. The traceability check on the final stage also exposed gaps in earlier work, which were then logged and fixed.
