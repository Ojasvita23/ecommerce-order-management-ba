# To-Be Process: OMS v2 Phase 1

## Purpose
Shows how the order lifecycle will work after OMS v2 Phase 1, based on BR-01 to BR-07, FR-01 to FR-07, the Business Rules, and UC-01 to UC-05. Gaps that are still undecided are shown as open (yellow, dashed) rather than resolved by assumption.

## Combined To-Be Diagram (Mermaid)

```mermaid
flowchart TD
    subgraph Customer["Customer"]
        A1["Browses, adds to cart, checks out"]
        A2["Requests a return - mechanism TBD"]
        A3["Provides return reason"]
        A4["Views order status with timestamp"]
    end

    subgraph System["System"]
        B1["Calls payment gateway, then order-creation API"]
        B2{"Order created?"}
        B3["Status set to Placed"]
        B4["Logs payment-order mismatch"]
        B5["Flags mismatch if unresolved past threshold"]
        B6["Status updated within 1 hour of each event"]
        B7["Picking list generated - Cancelled orders excluded"]
        B8["Order cancelled after list generated - persistent flag raised"]
        B9["Sellable stock incremented"]
        B10["Item recorded in damaged-stock count, excluded from sellable stock"]
        B11["Inspection result and return reason made visible to Finance"]
        B12["Refund initiated individually within 1 business day"]
        B13["Query recorded against the order, visible to both teams"]
    end

    subgraph Warehouse["Warehouse"]
        C1["Picks, packs, ships order"]
        C2["Pulls flagged order from packing or dispatch"]
        C3["Inspects returned item on arrival"]
        C4{"Inspection result?"}
        C5["Views and responds to query"]
    end

    subgraph Finance["Finance"]
        D1["Notified of mismatch within 24 hours"]
        D2{"Resolution path - OPEN, no default rule"}
        D3["Approves refund for mismatch"]
        D4["Requests manual order creation"]
        D5["Reviews return reason and inspection result"]
        D6{"Refund eligible?"}
        D7["Approves refund"]
    end

    subgraph Support["Support"]
        E1["Records query about an order"]
        E2["Views order status internally"]
    end

    subgraph OpenGaps["Open gaps - not resolved in Phase 1"]
        G1["GAP: courier not confirmed to know about cancellation after dispatch"]
        G2["GAP: refund eligibility when damage cause is unclear - policy undecided"]
        G3["GAP: disposal of damaged items - undecided"]
    end

    A1 --> B1
    B1 --> B2
    B2 -->|Yes| B3
    B2 -->|No| B4
    B4 --> B5
    B5 --> D1
    D1 --> D2
    D2 -->|Option A| D3
    D2 -->|Option B| D4
    D3 --> B12
    D4 --> B3

    B3 --> B6
    B6 --> A4
    B6 --> E2
    B3 --> B7
    B7 --> C1
    B7 -.->|cancelled after generation| B8
    B8 --> C2
    C1 -.->|if flag missed and order ships| G1

    A2 --> A3
    A3 -->|item transported by courier| C3
    C3 --> C4
    C4 -->|Resellable| B9
    C4 -->|Damaged| B10
    B9 --> B11
    B10 --> B11
    B10 -.-> G3
    B11 --> D5
    D5 --> D6
    D6 -->|Eligible| D7
    D6 -.->|Damage cause unclear| G2
    D7 --> B12

    E1 --> B13
    B13 --> C5

    classDef gap fill:#fff3cd,stroke:#d4a017,stroke-dasharray: 5 5,color:#000;
    class G1,G2,G3 gap;
```

## How to read this
- Solid arrows are decided, documented flow steps.
- Dotted arrows are branches that depend on a missed step or an unresolved question.
- Yellow dashed boxes are open gaps. They are carried forward honestly, not filled in.
- The Resolution path diamond in Finance (D2) is also open: the diagram shows both options because the default rule is still a pending business decision.

## What changed from As-Is

| Area | As-Is | To-Be |
|---|---|---|
| Order status | Shipped and Delivered updated manually by a daily CSV upload | Status updated within 1 hour of each event, visible to customer and internal teams (BR-02) |
| Payment without order | Silent failure; found by customer email or weekly Excel reconciliation | Mismatch logged, flagged, and Finance notified within 24 hours (BR-01) |
| Picking list | Generated once at 6 PM; late cancellations missed | Cancelled orders excluded at generation; late cancellations persistently flagged (BR-03) |
| Returns and stock | No stock increment logic; damaged items counted as sellable | Resellable restocked, damaged tracked separately (BR-04) |
| Return reason | Not captured | Captured at request, visible with inspection result to Finance (BR-05) |
| Refunds | Twice-weekly batch | Initiated individually within 1 business day of approval (BR-06) |
| Coordination | WhatsApp and manual Excel | Queries recorded against the order and retrievable (BR-07) |

## Gaps carried forward
1. Return-initiation mechanism (how a customer starts a return) is undefined.
2. Courier awareness of cancellation after dispatch is unconfirmed (P6a).
3. Refund eligibility depends on damage cause, and no policy or inspection process exists.
4. Disposal of damaged items is undecided.
5. Default resolution for payment-without-order (refund vs manual order) is undecided.
