# SuperNegotiate Cursor / Grok plugin

Install from the Cursor marketplace like Bird: **plugin → hosted MCP + skills**. Paste an `snk_` API key. There is no Mac tunnel and no in-app Assistant panel.

## Install (Bird-parity)

1. Cursor: **Plugins → SuperNegotiate** (or submit/install from [cursor.com/marketplace](https://cursor.com/marketplace)). Grok Bot: **Settings → Plugins**. Both clients load this package: `.cursor-plugin/` for Cursor and `.grok-plugin/` for Grok Bot, with the same hosted MCP URL and `snk_` key.
2. Paste **API key** (`snk_…`). Create one while signed in at [app.supernegotiate.com](https://app.supernegotiate.com): `POST https://api.supernegotiate.com/api/auth/api-keys`.
3. Leave **MCP server URL** at the default `https://api.supernegotiate.com/mcp` unless you self-host.
4. Ask the agent to create a sourcing event. It becomes the **category manager** (skill `category-manager`): monitor, remind, negotiate within your rules via Autopilot APIs, escalate award.

That is the whole setup. The agent calls the live SuperNegotiate API over MCP. The web cockpit at `/chat?event=<eventCode>` remains the source of truth for specs and documents — not an AI slide-over.

## Setup fields

| Field | Required | Default |
|---|---|---|
| `SUPERNEGOTIATE_API_KEY` | yes | — |
| `SUPERNEGOTIATE_MCP_URL` | no | `https://api.supernegotiate.com/mcp` |

## Skills

- `category-manager` — set-and-forget babysit (primary buyer skill)
- `run-sourcing-event` — create, invite, start, then hand off to category-manager
- `bid-on-event` — supplier join and bid
- `review-and-award` — compare, counter, award (used when escalating)

## Tools (hosted MCP)

The MCP server proxies the live SuperNegotiate HTTP API (`snk_` key or JWT):

| Tool | Role | API |
|---|---|---|
| `get_auth_setup` | any | (local guidance) |
| `health_check` | any | `GET /api/health` |
| `whoami` | any | `GET /api/auth/me` |
| `list_events` / `get_event` | buyer or supplier | `GET /api/auctions` |
| `create_event` / `start_event` | buyer | `POST /api/auctions`, `POST /:id/start` |
| `invite_supplier` / `resend_invitation` / `admit_supplier` | buyer | invitation routes |
| `join_event` / `place_bid` | supplier | `POST /join`, `POST /:id/bid` |
| `counter_offer` / `award_event` | buyer | offers routes |
| `list_messages` / `send_message` | participant | REST read + Socket.IO send |
| `upload_*` | role-aware | bid / event / chat files |
| `*_knockout_round*` | buyer | tournament rounds |
| `*_survey*` | role-aware | surveys (`status: active` sends) |
| `whatsapp_*` | premium + Meta env | status / link / test (not Twilio) |
| `*_negotiation_agent*` | buyer | Autopilot (set-and-forget engine) |
| `login` / `use_auth` | any | switch account without restart |
| `get_workspace` | buyer | `GET /api/organizations/me` |
| `list_contacts` | buyer | `GET /api/contacts` |

## Self-host / local

Override `SUPERNEGOTIATE_MCP_URL` (for example `http://127.0.0.1:5055/mcp`) and run `npm run mcp:http` against a local API. See [docs/mcp.md](../../docs/mcp.md).

## Local install (without marketplace)

Copy or symlink this folder to `~/.cursor/plugins/local/supernegotiate` and reload the window.

## Privacy

See [PRIVACY.md](PRIVACY.md). API keys stay in Cursor plugin config; MCP forwards them to SuperNegotiate as `Authorization: Bearer`.
