---
name: review-and-award
description: Compare SuperNegotiate bids, send a counter-offer, or award an event as a buyer. Use when the user wants a bid readout, a counter, a winner, or to close sourcing.
---

# Review bids and award (buyer)

Use the SuperNegotiate MCP tools. Confirm `whoami` returns `role: buyer`.

## Workflow

1. `list_events` (optionally `status: active`) to find the event, then `get_event`.
2. Summarize from the event payload — do not call endpoints that are not wrapped:
   - each bid: supplier, amount, time, line-item breakdown if present
   - `joinedSuppliers` vs `admittedSuppliers`
   - event `status` and any `awardStatus`
3. If a supplier has joined but cannot bid, `admit_supplier`.
4. To push a supplier lower, `counter_offer` with `supplierId` and `amount` (optional `comment`, optional `attachments` as base64 files). This is a buyer offer, not a supplier bid.
5. Read/send event chat with `list_messages` / `send_message`. Start or execute knockout rounds if the event uses them.
6. To close, `award_event` with `winnerId` set to a joined, eligible supplier. Optional `totalAmount`, `notes`, `buyerMessage`.
7. Only active, ended, or paused events can be awarded. Draft/cancelled/completed will 400.
8. After award, re-fetch `get_event` and report `awardStatus` (pending supplier acceptance is possible). Give the cockpit link `https://app.supernegotiate.com/chat?event=<eventCode>` — not an in-app Assistant.

## Decision hygiene

- State the lowest bid and any line-item caveats before recommending a winner.
- If `baselinePrice` exists, compare the likely award against it; otherwise say savings are spread-based in SuperNegotiate analytics.
- Do not award a disqualified, eliminated, or blocked supplier.
- Category-manager uses this skill when escalating a set-and-forget event to a human award decision.
