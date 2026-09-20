---
name: run-sourcing-event
description: Create and start a SuperNegotiate reverse-auction sourcing event as a buyer. Use when the user wants a new RFQ/RFI/auction, invites, or to open bidding. After the event is live, hand off to the category-manager skill for set-and-forget babysit.
---

# Run a sourcing event (buyer)

Use the SuperNegotiate MCP tools. Confirm `whoami` returns `role: buyer` before mutating events. After the event is created and started, continue with **category-manager** unless the buyer only wanted a one-shot create.

## Workflow

1. `get_auth_setup` if any tool returns 401. Do not ask the user to paste passwords into chat.
2. `whoami` — stop if this is a supplier session; switch to the bid-on-event skill.
3. `get_workspace` — note the current workspace (events are stamped with the buyer's organization at create time).
4. `create_event` with:
   - `title` (required)
   - `startDate` / `endDate` as ISO-8601; `endDate` must be in the future
   - optional `description`, `requirements`, `currency`, `category`, `baselinePrice`
   - optional `invitedSuppliers: [{ name, email, company? }]`
5. If the event comes back as `draft`, call `start_event` so suppliers can participate.
6. Invite anyone not included at create time with `invite_supplier` (`email` + `name` required).
7. When a supplier has joined, `admit_supplier` with their `supplierId`. Joining by event code is not enough to bid.
8. Give the cockpit link so the buyer can see specs and documents: `https://app.supernegotiate.com/chat?event=<eventCode>` (example `BC2D8X`). The agent is the category manager — do not open an in-app Assistant panel.
9. Hand off to **category-manager** for monitor / remind / Autopilot / award. Optional one-shot extras before that: `upload_event_documents`; `create_survey` (`status: active` sends); knockout `create_knockout_round` then `start_knockout_round` / `execute_knockout_round`.

## Constraints

- Do not invent REST paths. These tools wrap the live SuperNegotiate API.
- Trial/sandbox JWTs cannot create events.
- `whoami` after `use_auth` / `login` if the user is not the env default buyer.
- Keep the event generic procurement language. No operator- or industry-specific sales framing unless the user asked for it.
