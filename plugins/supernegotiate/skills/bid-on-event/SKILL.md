---
name: bid-on-event
description: Join a SuperNegotiate event as a supplier and place reverse-auction bids. Use when the user has an event code, was invited, or wants to submit or improve a bid.
---

# Bid on an event (supplier)

Use the SuperNegotiate MCP tools. Confirm `whoami` returns `role: supplier` before joining or bidding.

## Workflow

1. `whoami` — if the session is a buyer, do not place bids; use run-sourcing-event instead.
2. `join_event` with the buyer’s `eventCode` (case-insensitive; the API uppercases it).
3. `get_event` with the returned event id. Read title, requirements, line items, current status, and whether you are in `admittedSuppliers`.
4. If you are not admitted, tell the user the buyer must admit you. Do not retry `place_bid` in a loop.
5. `place_bid` with a numeric `amount` > 0. This is a reverse auction: the new amount must be strictly lower than your current best bid.
6. Optional: `notes`, `lineItemBids` (`[{ lineItemIndex, price }]`) for line-item events, `fieldValues` for custom bid fields. Attach files with `upload_bid_documents` before or after bidding.
7. If the API returns 409 asking to confirm an unusual amount, show the warning and only resubmit with `confirmUnusualAmount: true` after the user agrees.
8. Chat with the buyer via `list_messages` / `send_message` (supplier messages are not broadcast to rivals).
9. Re-read `get_event` to confirm the bid landed.
10. If `whoami` is a buyer, call `use_auth` or `login` with the supplier credentials first.

## Constraints

- Never bid above a walk-away or floor the user stated.
- Do not inspect or quote other suppliers’ identities if the payload is redacted.
- `get_workspace` and `create_event` are buyer-only; a 403 is expected for suppliers.
