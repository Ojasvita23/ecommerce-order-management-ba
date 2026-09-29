# Business Objectives — OMS v2

## Purpose
Define measurable, time-bound outcomes for OMS v2 Phase 1, derived from stakeholder interviews and synthesized pain points. Each objective is written to be SMART (Specific, Measurable, Achievable, Relevant, Time-bound).

## Objectives

| ID | Objective | Baseline | Target | Deadline |
|---|---|---|---|---|
| O1 | Reduce "where is my order" support tickets | ~40% of total tickets (Support Lead's estimate — to be verified via ticket-tagging data) | ≤20% of total tickets | Within 3 months of launch |
| O2 | Eliminate charged-but-no-order cases left unresolved | ~60/month, currently resolved only via weekly manual reconciliation | 0 cases unresolved beyond 24 hours | Within 3 months of launch |
| O3 | Make refund timing predictable | Refunds processed in a twice-weekly batch; no committed customer-facing timeline | 95% of refunds initiated within 1 business day of approval | Within 3 months of launch |
| O4a | Establish inventory accuracy baseline | Unknown | Baseline figure obtained via warehouse cycle count | Within 2 weeks of project start |
| O4b | Improve inventory accuracy | [Baseline from O4a] | [Baseline] + X% (target set after O4a completes) | Within 3 months of go-live |
| O5a | Establish baseline for cancelled-but-shipped orders | Unknown | Baseline figure obtained via dispatch log review (2-week sample) | Within 2 weeks of project start |
| O5b | Reduce cancelled-orders-shipped incidents | [Baseline from O5a] | Zero, or near-zero if data shows an unavoidable residual rate (e.g., cancellations after courier pickup cutoff) | Within 3 months of go-live |
| O6 | Replace informal Support-Warehouse coordination | ~0% — coordination happens via untracked WhatsApp messages | 100% adoption of a tracked, auditable coordination channel | Within 3 months of launch |

## Notes on Methodology
- O4 and O5 were split into a baseline sub-objective (a) and a target sub-objective (b), rather than assigning an unverified target upfront. Setting a target (e.g., "≥98% accuracy") without a known baseline would be an unjustified guess, not a measurable objective.
- Each objective is scoped to what OMS v2 can directly influence. For example, O3's target addresses ShopKart's internal refund-initiation delay (the batch cycle), separate from bank clearing time, which is outside the project's control.
- Objectives intentionally avoid naming a specific technical solution (e.g., O2 does not prescribe "auto-retry" vs. "auto-refund" — that decision is deferred to requirements/design stage, informed by business policy input).
