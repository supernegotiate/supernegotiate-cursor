# SuperNegotiate MCP and Cursor plugin

SuperNegotiate installs in Cursor and Grok Bot **like Bird**: marketplace plugin → hosted MCP + skills → paste an `snk_` API key → the agent is the category manager. There is no Mac tunnel and no in-app Assistant panel.

| Piece | Path |
|---|---|
| Hosted MCP (production) | `https://api.supernegotiate.com/mcp` |
| MCP server source | [`mcp/`](../mcp/) |
| Cursor / Grok plugin | [`plugins/supernegotiate/`](../plugins/supernegotiate/) |
| Cursor manifest | [`plugins/supernegotiate/.cursor-plugin/plugin.json`](../plugins/supernegotiate/.cursor-plugin/plugin.json) |
| Grok manifest | [`plugins/supernegotiate/.grok-plugin/plugin.json`](../plugins/supernegotiate/.grok-plugin/plugin.json) |
| Marketplace publish checklist | [`docs/marketplace-publish.md`](marketplace-publish.md) |
| Cursor marketplace index | [`.cursor-plugin/marketplace.json`](../.cursor-plugin/marketplace.json) |
| Grok marketplace index | [`.grok-plugin/marketplace.json`](../.grok-plugin/marketplace.json) |

## 1. Marketplace install (what end users do)

1. Install **SuperNegotiate** from [cursor.com/marketplace](https://cursor.com/marketplace) (Grok Bot: **Settings → Plugins**).
2. Create a personal API key while signed in at [app.supernegotiate.com](https://app.supernegotiate.com):

```bash
curl -s https://api.supernegotiate.com/api/auth/login \
  -H 'Content-Type: application/json' \
  -d '{"email":"you@company.com","password":"…"}'

curl -s https://api.supernegotiate.com/api/auth/api-keys \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  -H 'Content-Type: application/json' \
  -d '{"name":"Cursor MCP"}'
```

3. Paste the returned `key` (`snk_…`) into the plugin. Leave **MCP server URL** at `https://api.supernegotiate.com/mcp`.
4. Ask the agent to create a sourcing event and babysit it (skill `category-manager`).

Trial/sandbox sessions cannot mint keys. Send the key as `Authorization: Bearer snk_…` or `X-API-Key`. JWT access tokens expire in **1 hour**; prefer API keys for unattended agents.

## 2. How hosted MCP is deployed

The plugin talks to a **public Streamable HTTP** server. End users never run `npm run mcp:http` on a laptop.

**Preferred — same origin as the production API** (`POST https://api.supernegotiate.com/mcp`):

- Express mounts MCP **before** body parsers (`server/src/mcpHttpMount.ts`).
- In-process: Node loads `server/mcp-dist` (built by `server/scripts/build-mcp.cjs`) and the MCP client calls loopback `http://127.0.0.1:$PORT` (same app).
- Production Railway (`api.supernegotiate.com`, service root `/server`): `server/railway.json` build is `npm install && node scripts/build-mcp.cjs && npx tsc`, start remains `node dist/server.js`. The `/server` image does not contain repo-root `mcp/`; sources are vendored at `server/mcp/` (sync with `node server/scripts/sync-embedded-mcp.cjs`).
- Hosted `GET https://api.supernegotiate.com/mcp` is live (`status: ok`, `name: supernegotiate-mcp`). The response `apiUrl` is the in-process loopback (`http://127.0.0.1:5000`), not a URL buyers configure.
- Sidecar: set `MCP_UPSTREAM=https://<mcp-service>` on the API so `/mcp` reverse-proxies that process.
- Opt out: `ENABLE_MCP_HTTP=0`.

**Alternative — always-on MCP service** (`mcp/`):

```
NODE_ENV=production
MCP_HOSTED=1
MCP_HTTP_HOST=0.0.0.0
PORT=<Railway port>
SUPERNEGOTIATE_API_URL=https://api.supernegotiate.com   # default when hosted
Start: node dist/index.js --http
```

Hosted mode binds `0.0.0.0` and defaults `SUPERNEGOTIATE_API_URL` to production. Per-request `Authorization` from the plugin overrides process env (multi-user).

## 3. Local / self-host (developers)

```bash
cd mcp
cp .env.example .env
# SUPERNEGOTIATE_API_URL=http://localhost:5000
# SUPERNEGOTIATE_API_KEY=snk_…
npm install
npm run build
npm run start:stdio          # stdio (spawned by the client)
npm run start:http           # http://127.0.0.1:5055/mcp
npm test && npm run smoke
```

Root scripts: `npm run mcp`, `npm run mcp:http`, `npm run mcp:smoke`. Override the plugin MCP URL to the local endpoint.

HTTP binds `127.0.0.1` unless hosted (`NODE_ENV=production` or `MCP_HOSTED=1`). Set `MCP_HTTP_HOST=0.0.0.0` only behind TLS.

**Project `mcp.json`** (stdio, no marketplace plugin):

```json
{
  "mcpServers": {
    "supernegotiate": {
      "command": "node",
      "args": ["/absolute/path/to/mcp/dist/index.js", "--stdio"],
      "env": {
        "SUPERNEGOTIATE_API_URL": "http://localhost:5000",
        "SUPERNEGOTIATE_API_KEY": "snk_your_key"
      }
    }
  }
}
```

Remote HTTP (hosted or local):

```json
{
  "mcpServers": {
    "supernegotiate": {
      "url": "https://api.supernegotiate.com/mcp",
      "headers": {
        "Authorization": "Bearer ${env:SUPERNEGOTIATE_API_KEY}"
      }
    }
  }
}
```

## 4. Claude Desktop / Claude Code

Claude Desktop (`claude_desktop_config.json`) can spawn stdio as above, or point at the hosted URL with a Bearer `snk_` key (same JSON as remote HTTP).

macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`  
Windows: `%APPDATA%\Claude\claude_desktop_config.json`

Do not commit live keys. The Cursor plugin uses `${SUPERNEGOTIATE_API_KEY}` variables instead.

## 5. ChatGPT

ChatGPT cannot spawn local stdio. Add a remote MCP server URL (`https://api.supernegotiate.com/mcp`) with `Authorization: Bearer snk_…`. Custom GPT Actions can also call the REST API directly.

## 6. Generic MCP clients

```
POST https://api.supernegotiate.com/mcp
Accept: application/json, text/event-stream
Authorization: Bearer snk_…
```

The HTTP server is stateless (new transport per request). stdio remains available for local agents.

## 7. Agent skills

Under `plugins/supernegotiate/skills/`:

- `category-manager` — set-and-forget babysit (monitor, remind, Autopilot, escalate award)
- `run-sourcing-event` — buyer create / invite / start, then hand off
- `bid-on-event` — supplier join / bid / bid documents / chat
- `review-and-award` — compare, counter, award

## 8. Switching accounts (Grok Bot / Cursor)

Env `SUPERNEGOTIATE_API_KEY` is the **default** identity. To act as another buyer or as a supplier:

1. **HTTP MCP (marketplace plugin)** — send `Authorization: Bearer <that user's snk_ key or JWT>` on each request. The plugin already forwards `${SUPERNEGOTIATE_API_KEY}`. Per-request Bearer **overrides** the process env.
2. **stdio** — call `use_auth` with `{ apiKey }` or `{ accessToken }`, or `login` with email/password. `use_auth({ clear: true })` restores env defaults. `whoami` confirms the new role.
3. Mint a key per account (`POST /api/auth/api-keys` while logged in as that user). Do not paste passwords into chat if a key exists.

Event chat send uses **Socket.IO** `handshake.auth.token` with the same JWT or `snk_` key as HTTP.

## 9. Web cockpit (source of truth for humans)

Chat (Grok / MCP) runs actions. The buyer still looks at `/chat` for specs, documents, team, invites, and bids:

```
https://app.supernegotiate.com/chat?event=BC2D8X
https://app.supernegotiate.com/chat?event=<auctionId>
```

`event` accepts the Mongo id **or** the 4–12 character `eventCode`. The agent is the category manager in Cursor/Grok; do not rely on an in-app Assistant slide-over.

## 10. Remaining gaps

- Technical evaluation uploads, PDF/Excel report downloads
- WhatsApp inbound webhooks (suppliers text Meta; not an MCP tool)
- Admin-only `POST /api/admin/whatsapp/send-message`

**WhatsApp is Meta Cloud API, not Twilio.** The SuperNegotiate server needs `WHATSAPP_ACCESS_TOKEN`, `WHATSAPP_PHONE_NUMBER_ID`, `WHATSAPP_BUSINESS_ACCOUNT_ID`, and `WHATSAPP_APP_SECRET`. `whatsapp_status.configured` is false until those are set.

Autopilot APIs stay on the server (`/api/negotiation-agents`). Agents call them through MCP (`create_negotiation_agent`, `start_negotiation_agent`, `list_live_negotiations`, …).
