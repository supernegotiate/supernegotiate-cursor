# SuperNegotiate for Cursor / Grok Bot

Public **marketplace plugin only**. Install like Bird: paste an `snk_` API key → talk to the hosted MCP → the agent runs sourcing as your category manager (create event, babysit, negotiate within rules, escalate award).

- **Hosted MCP:** https://api.supernegotiate.com/mcp  
- **Plugin path:** `plugins/supernegotiate`  
- **Product app:** private — not in this repository

## Install (after marketplace listing)

1. Cursor / Grok Bot → Plugins → SuperNegotiate  
2. Paste your personal API key (`snk_…`) from SuperNegotiate (`POST /api/auth/api-keys` while signed in)  
3. Leave MCP URL at the default unless you self-host  

Then: “Create an RFQ for … and babysit it until award.”

## Publish

Submit this repo at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish).

See [docs/marketplace-publish.md](docs/marketplace-publish.md).
