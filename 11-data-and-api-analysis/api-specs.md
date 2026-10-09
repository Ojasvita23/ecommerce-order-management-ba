# API Specifications: OMS v2 Phase 1

## Purpose
Business-level contracts for the four highest-value APIs from `api-inventory.md`: Order Creation (API-02), Payment Confirmation Webhook (API-03), Order Status Update (API-05) and Refund Initiation (API-11). Each spec defines what data goes in, what comes out, how failures behave, and what Engineering must confirm. Field names follow `data-model.md`. Data types, auth scheme and URL design are left to technical design.

---

## API-02: Order Creation
**Purpose:** Create an order once payment has succeeded, and make the outcome unambiguous to the caller.
**Traces to:** BR-01, FR-01.1, P2, A3
**Caller / Provider:** Checkout (caller) to OMS Order Service (provider)
**Trigger:** Payment confirmed successful at checkout, or a Finance-requested manual order (UC-01, step 6b)

**Request**
| Field | Required | Notes |
|---|---|---|
| Customer ID | Yes | |
| Payment ID / gateway transaction reference | Yes | **New requirement:** links the order to its payment so mismatches are detectable (ERD: Payment `||--o|` Order) |
| Order items (Product ID, quantity, price at purchase) | Yes | Price is captured at purchase, per ERD |
| Idempotency key | Yes | Same key on retry must return the same order, never a second one |

**Response**
| Outcome | Returns |
|---|---|
| Success | Order ID, status = Placed, Placed date/time |
| Duplicate request (same idempotency key) | The original order, not a new one |
| Failure | Explicit failure reason. **Never a silent timeout** |

**Error scenarios**
| Scenario | Required behaviour |
|---|---|
| Timeout / no response | Caller treats the result as unknown, not failed. Payment-order mismatch is logged (FR-01.1) |
| Validation failure (e.g. product out of stock) | Explicit rejection with reason. Triggers the open policy decision: complete anyway or refund (Pending Business Rule 2, scope question C2) |
| Payment ID already linked to an order | Reject as duplicate, return the existing order |

**Non-functional:** must meet NFR-07 (no duplicate orders under concurrent calls).
**Open items for Engineering:** failure modes beyond timeout (A3); whether the existing API can accept an idempotency key.

---

## API-03: Payment Confirmation Webhook
**Purpose:** Give OMS an independent signal that a payment succeeded, so a failed order-creation call can no longer hide a charge.
**Traces to:** BR-01, FR-01.1, FR-01.2, K3, P2
**Caller / Provider:** Payment Provider (caller) to OMS (provider)
**Trigger:** Gateway records a payment event (success, failure, refund)

**Request (payload from gateway)**
| Field | Notes |
|---|---|
| Gateway transaction reference | Matches Payment record |
| Event type | Payment succeeded / failed / refunded |
| Amount, payment date/time, payment method | Stored on Payment (ERD) |
| Signature | OMS must verify the call genuinely came from the gateway |

**Response:** acknowledgement only. OMS confirms receipt so the gateway stops retrying.

**Required behaviour**
- On a "payment succeeded" event, OMS checks whether an order exists for that reference. If none exists after the defined threshold, it logs a Payment-Order Mismatch (FR-01.1, FR-01.2).
- Duplicate deliveries of the same event must be ignored (idempotent on transaction reference + event type).
- Events may arrive out of order or before the checkout call finishes. The mismatch check must wait for the threshold rather than flag instantly (avoids false mismatches).
- Failed verification is rejected and logged, never processed.

**Error scenarios**
| Scenario | Required behaviour |
|---|---|
| OMS is down when the webhook is sent | Gateway retries. Retry schedule must be confirmed (see questions) |
| Unknown transaction reference | Record as unmatched payment, treat as potential mismatch |
| Signature invalid | Reject, log attempt (NFR-03) |

**Open items for Engineering:** does the gateway support webhooks, signing and retries at all? (K3, A3). This is the single biggest feasibility question in Phase 1.

---

## API-05: Order Status Update
**Purpose:** Replace the once-daily manual CSV upload with status changes recorded within 1 hour of the real event.
**Traces to:** BR-02, FR-02.1, FR-02.2, UC-04, EC-05, K4, A6, P1
**Caller / Provider:** Warehouse tooling or Logistics Partner event feed (caller) to OMS Order Service (provider)
**Trigger:** A physical event occurs: picked, shipped, delivered

**Request**
| Field | Required | Notes |
|---|---|---|
| Order ID | Yes | |
| New status | Yes | Must be one of the 5 permitted values (K4) |
| Event date/time | Yes | When it actually happened, not when it was sent. Needed to measure the 1-hour target |
| Source | Yes | Warehouse or Courier. Supports audit (NFR-05) |

**Response**
| Outcome | Returns |
|---|---|
| Success | Order ID, new status, status last-updated date/time |
| Rejected | Reason (invalid transition, unknown order) |

**Rules**
- Only valid transitions are accepted (e.g. Placed to Processing to Shipped to Delivered). A Cancelled order cannot move to Shipped. This also guards BRule-03.
- Repeating the same update is harmless (idempotent) and does not change the timestamp.
- Out-of-order events (Delivered arriving before Shipped) are rejected or held, not silently applied. Exact handling is a design decision.
- A successful update must be visible to the customer and to internal teams within the 1-hour window (FR-02.1, FR-02.2).

**Open items for Engineering:** courier push vs poll; what else writes to status today (A6); how EC-05's "within 1 hour" is guaranteed (event-driven vs frequent polling).

---

## API-11: Refund Initiation
**Purpose:** Start an approved refund individually within 1 business day, exactly once, with every attempt logged.
**Traces to:** BR-06, FR-06.1, FR-06.2, BRule-01, EC-04, NFR-02, NFR-03, NFR-07, A1, A2
**Caller / Provider:** OMS Refund Service (caller) to Payment Provider refund API (provider), after Finance approval
**Trigger:** Finance approves a refund from a return, a pre-shipment cancellation, or a resolved mismatch

**Request**
| Field | Required | Notes |
|---|---|---|
| Source (Return / Cancellation / Mismatch) | Yes | |
| Source record ID | Yes | Exactly one source per refund (ERD constraint) |
| Original gateway transaction reference | Yes | Refund goes to the original method (A1) |
| Amount | Yes | Must not exceed the original payment |
| Approved by | Yes | Must hold the Finance role (BRule-01) |
| Idempotency key | Yes | Prevents the duplicate refund in EC-04 |

**Response**
| Outcome | Returns |
|---|---|
| Success | Refund ID, status = Initiated, initiated date/time |
| Duplicate (same source already refunded) | The existing refund, no new money movement |
| Failure | Reason, status = Failed, retry scheduled |

**Error scenarios**
| Scenario | Required behaviour |
|---|---|
| Approver is not Finance | Reject, log |
| Source already has a refund | Reject as duplicate (EC-04, NFR-07) |
| Gateway call fails or times out | Automatic retry (NFR-02). Every attempt logged with outcome (NFR-03). Never fail silently |
| Original payment method can't accept a refund (expired card) | Refund marked Failed and surfaced to Finance. Fallback mechanism is undesigned (A1) |

**Scope note:** the 1-business-day target covers initiation only. Bank settlement of 3-5 days is outside ShopKart's control and is not tracked (A2).
**Open items:** FR-06.1 says the system initiates the refund, BRule-01 says Finance initiates it. The two documents need alignment. This spec assumes Finance approves and the system initiates.

---

## Cross-cutting rules
- **Idempotency** applies to API-02, 03, 05 and 11. This one rule closes EC-01, EC-04 and most of NFR-07.
- **Audit:** every call records who or what made it, and when (NFR-03, NFR-04, NFR-05).
- **No silent failure:** every error path above ends in an explicit response, a retry, or a logged record. This is the principle behind the whole project (P2).

## Questions for Engineering
1. Can existing APIs accept an idempotency key, or does that need building?
2. Does the gateway support signed webhooks with retries, and a refund API with idempotency?
3. What is the valid status-transition model today, and who enforces it?
4. Does the courier offer delivery events by push, or only pull?

## Notes on Methodology
- Fields were derived from ERD attributes so the data model and API specs cannot drift apart.
- Specs define behaviour and failure handling, not protocol details. HTTP methods, auth and payload formats stay with Engineering.
- Open items are carried forward from existing gaps instead of being resolved by assumption.
