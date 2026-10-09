# Integration Risks: OMS v2 Phase 1

## Purpose
Identifies what can go wrong at the integration points listed in `api-inventory.md` and specified in `api-specs.md`. Each risk has a likelihood and impact rating, a proposed mitigation (stated as a need, not an implementation), and an owner for the decision. This file feeds the project-level Risks and Dependencies document.

## Rating scale
- **Likelihood:** High / Medium / Low, based on current evidence (interviews, assumptions)
- **Impact:** High = money lost or customer harmed; Medium = delay or manual workaround; Low = inconvenience

## Integration Risks

| ID | Risk | APIs | Likelihood | Impact | Evidence / Traces to | Mitigation (need) | Decision owner |
|---|---|---|---|---|---|---|---|
| IR-01 | **Gateway does not support webhooks, or supports them without signing/retries.** The core fix for P2 cannot be built as designed | API-03 | Medium | High | K3, A3, P2 | Confirm gateway capability before design starts. Fallback: scheduled reconciliation against the gateway's transaction report, which is slower but still automated | Engineering Lead, Payment Provider |
| IR-02 | **Duplicate or out-of-order webhook delivery** causes false mismatches or double processing | API-03 | High | Medium | API-03 spec, NFR-07 | Idempotent handling on transaction reference + event type. Mismatch check waits for the threshold before flagging | Engineering Lead |
| IR-03 | **Order API retry creates duplicate orders** because it has no idempotency key today | API-02 | Medium | High | EC-01, NFR-07, Engineering interview ("we don't retry") | Require an idempotency key. Same key returns the same order. Must be built before any retry logic is added | Engineering Lead |
| IR-04 | **Duplicate refund** from two Finance approvals, or a retried gateway call | API-11 | Medium | High | EC-04, NFR-07 | One refund per source record, enforced at the data level, plus an idempotency key on the gateway call | Engineering Lead, Finance |
| IR-05 | **Refund call fails repeatedly** and nobody notices (a repeat of the silent-failure pattern) | API-11, API-04 | Medium | High | NFR-02, NFR-03, A1 | Retries with every attempt logged. After the retry limit, surface the case to Finance as a visible exception | Engineering Lead, Finance |
| IR-06 | **Original payment method cannot take a refund** (expired or closed card). No fallback exists | API-11 | Medium | Medium | A1 | Mark the refund Failed and surface to Finance. Fallback mechanism needs a business decision | Finance, Product Manager |
| IR-07 | **Courier does not provide delivery events by push**, so the 1-hour status target needs polling or stays manual | API-05 | Medium | High | FR-02.1, EC-05, P1 | Confirm courier capability. If pull-only, define polling frequency and check it can meet the 1-hour window | Engineering Lead, Logistics Partner |
| IR-08 | **Unknown writers to the order status field** (other than the cron job and CSV upload) overwrite the new real-time updates | API-05 | Low-Medium | High | A6, K4 | Audit everything that writes to status before build. Enforce valid transitions in one place | Engineering Lead |
| IR-09 | **Invalid status transitions** (e.g. Cancelled moved to Shipped) are accepted | API-05, API-07 | Medium | High | BRule-03, P6 | Transition rules enforced at the service, not left to callers | Engineering Lead |
| IR-10 | **Cancellation is not as instant or reliable as assumed**, so exclusion from picking can't rely on it | API-07, API-08 | Low-Medium | High | A4, FR-03.1 | Verify end-to-end cancellation behaviour before building on it. If unreliable, fix cancellation first | Engineering Lead |
| IR-11 | **Race condition at list generation:** an order cancelled at the exact moment the list is built | API-08 | Medium | Medium | EC-03 | Define a clear cutoff (e.g. status is read at one moment inside one consistent step). Anything cancelled after is caught by the persistent flag | Engineering Lead |
| IR-12 | **Concurrent inventory updates** cause double-increment for one return | API-09 | Medium | High | NFR-07, P8 | One inspection result per return (ERD constraint), applied exactly once | Engineering Lead |
| IR-13 | **Courier never learns of a post-dispatch cancellation** and delivers the order | API-13 | Unknown | High | P6a, EC-02 | Not solved in Phase 1. Needs confirmation with the Logistics Partner and a business decision on recovery. Carried forward as an open risk | Operations Manager, Logistics Partner |
| IR-14 | **Return-initiation mechanism undefined**, so the return flow and API-10 cannot be built | API-10 | Certain (it is a known gap) | High | P9a, FR-05.1 | Decision needed from Product/Operations before design of the return flow starts | Product Manager, Operations Manager |
| IR-15 | **Finance notification channel undecided or unreliable** | API-04 | Medium | Medium | FR-01.3, NFR-02 | Choose a channel, retry on failure, log attempts, escalate unacknowledged cases (see UC-01 E1) | Product Manager, Finance |
| IR-16 | **Load growth degrades scheduled jobs** (mismatch detection, status updates) as orders increase beyond ~15,000/month | API-04, API-05 | Low | Medium | NFR-01, NFR-06 | Define performance targets and test against projected volume | Engineering Lead |
| IR-17 | **Unanswered cross-team queries** pile up with no escalation | API-12 | Medium | Low | EC-06, BR-07 | Define an escalation rule (time limit, then notify the team lead) | Operations Manager |

## Risks that need a decision before design can start
These block work and should go to the Product Manager and Engineering Lead first:
1. **IR-01:** gateway webhook capability (blocks API-03, the core fix for P2)
2. **IR-07:** courier event capability (blocks the 1-hour status target)
3. **IR-14:** return-initiation mechanism (blocks API-10)
4. **IR-03 / IR-04:** idempotency support on existing APIs (blocks safe retries)

## External dependencies
| Dependency | What we need | Risks |
|---|---|---|
| Payment Provider | Webhooks, signed events, refund API, idempotency support, settlement SLA (A2) | IR-01, IR-02, IR-04, IR-06 |
| Logistics Partner | Delivery events by push or pull, and awareness of cancellations | IR-07, IR-13 |

## Notes on Methodology
- Risks were found by asking, for each API: what if it is slow, fails, runs twice, runs out of order, or isn't what we assumed?
- Ratings are judgment calls based on interview evidence, not measured data. Where evidence is missing (IR-13) the likelihood is "Unknown" instead of a guess.
- Mitigations state the need, not the technology, consistent with the "what, not how" approach used throughout.
- Several risks here (IR-01, IR-07, IR-14) will become top entries in the project-level Risks and Dependencies document.
