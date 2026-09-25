# SuperNegotiate for Cursor / Grok Bot

Public **marketplace plugin only**. Install like Bird: paste an `snk_` API key → talk to the hosted MCP → the agent runs sourcing as your category manager (create event, babysit, negotiate within rules, escalate award).

- **Hosted MCP:** https://api.supernegotiate.com/mcp  
- **Plugin path:** `plugins/supernegotiate` (`.cursor-plugin/` and `.grok-plugin/`)  
- **Product app:** private — not in this repository

## Install (after marketplace listing)

1. Cursor / Grok Bot → Plugins → SuperNegotiate  
2. Paste your personal API key (`snk_…`) from SuperNegotiate (`POST /api/auth/api-keys` while signed in)  
3. Leave MCP URL at the default unless you self-host  

Then: “Create an RFQ for … and babysit it until award.”

## Publish

Submit this repository at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish). Paste:

**https://github.com/supernegotiate/supernegotiate-cursor**

Listing is free. Cursor reviews every listing and update manually. Grok Bot loads the same marketplace plugin (`plugins/supernegotiate/.grok-plugin/` matches the Cursor MCP URL and `snk_` key variables).

See [docs/marketplace-publish.md](docs/marketplace-publish.md).
