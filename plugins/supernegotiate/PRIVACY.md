# SuperNegotiate plugin privacy

This plugin does not store secrets in the repository. You set `SUPERNEGOTIATE_API_KEY` in Cursor / Grok (**Plugins → Configure**). Cursor substitutes `${SUPERNEGOTIATE_API_KEY}` into the MCP `Authorization` header.

## What is sent

- MCP tool calls go to the hosted Streamable HTTP endpoint (`https://api.supernegotiate.com/mcp` unless you override `SUPERNEGOTIATE_MCP_URL`).
- That server proxies the same SuperNegotiate REST/Socket.IO APIs the web app uses (events, invites, bids, chat, Autopilot).
- The API key or JWT is sent as `Authorization: Bearer` (or `X-API-Key`) to SuperNegotiate only.

## What is not sent

- The plugin does not train models on your events.
- Keys are not logged by the MCP process. Do not paste `snk_` keys into chat.
- Trial/sandbox sessions cannot mint API keys.

## Data controller

SuperNegotiate (`hi@supernegotiate.com`). Production app: https://app.supernegotiate.com. Production API: https://api.supernegotiate.com.
