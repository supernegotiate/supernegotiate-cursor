# Cursor marketplace publish checklist

Submit this public repo at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish):

**https://github.com/supernegotiate/supernegotiate-cursor**

Listing is free. Cursor reviews every listing and update manually. The same listing is what Grok Bot loads under **Settings → Plugins**. Install matches Bird: **marketplace plugin → hosted MCP + skills → paste `snk_` key → ready**. The agent is the category manager. The web UI is the cockpit. Not an in-app Assistant panel.

## Must be true before you submit

### Hosted MCP URL (no Mac tunnel)

- [x] Production API serves Streamable HTTP MCP at **`https://api.supernegotiate.com/mcp`**
  - Verified `GET` (no `Accept: text/event-stream`) returns `{"status":"ok","name":"supernegotiate-mcp","mcp":"/mcp",...}` from `railway-hikari`.
  - The JSON also includes `"apiUrl":"http://127.0.0.1:5000"`. That is the in-process loopback the hosted mount uses to reach the same API. Buyers leave the plugin URL at `https://api.supernegotiate.com/mcp`.
- [x] `GET https://api.supernegotiate.com/api/health` returns `{"status":"ok","db":"connected","message":"Server is running"}`
- [x] Plugin default `SUPERNEGOTIATE_MCP_URL` is that hosted URL (`plugins/supernegotiate/.cursor-plugin/plugin.json` and `plugins/supernegotiate/.grok-plugin/plugin.json`)

### Manifest

- [x] `plugins/supernegotiate/.cursor-plugin/plugin.json` — unique kebab-case `name`, description, version `1.2.1`, logo, **variables** for `SUPERNEGOTIATE_API_KEY` (required) and `SUPERNEGOTIATE_MCP_URL` (optional, production default)
- [x] `plugins/supernegotiate/.grok-plugin/plugin.json` — same MCP variables, logo, and skills; description tuned for Grok Bot buyers (category manager / set-and-forget sourcing)
- [x] `plugins/supernegotiate/.grok-plugin/mcp.json` — HTTP MCP, same `${SUPERNEGOTIATE_MCP_URL}` and `Authorization: Bearer ${SUPERNEGOTIATE_API_KEY}` as the Cursor `mcp.json`
- [x] `plugins/supernegotiate/plugin.json` — Agent Plugins manifest (Bird-shaped)
- [x] `plugins/supernegotiate/mcp.json` — HTTP MCP with `${SUPERNEGOTIATE_MCP_URL}` and `Authorization: Bearer ${SUPERNEGOTIATE_API_KEY}`
- [x] Repo-root `.cursor-plugin/marketplace.json` lists `source: plugins/supernegotiate`
- [x] Repo-root `.grok-plugin/marketplace.json` lists `source.path: ./plugins/supernegotiate` so a Grok marketplace add of this repo resolves the plugin package
- [x] Every `${VAR}` in both `mcp.json` files is declared under `variables`
- [x] Paths are relative (no `..`, no absolute paths)
- [x] No secrets committed

### Logo, skills, README, privacy

- [x] `plugins/supernegotiate/assets/logo.svg` committed and referenced as `assets/logo.svg` from both plugin manifests
- [x] Skills with valid `SKILL.md` frontmatter: `category-manager`, `run-sourcing-event`, `bid-on-event`, `review-and-award`
- [x] `plugins/supernegotiate/README.md` documents marketplace install (paste `snk_` key) — not localhost MCP
- [x] `plugins/supernegotiate/PRIVACY.md` — what the key is used for, production hosts `https://app.supernegotiate.com` and `https://api.supernegotiate.com`
- [ ] Full Cursor GUI install (symlink this folder to `~/.cursor/plugins/local/supernegotiate`) is a developer-machine check. Manifest JSON, skill frontmatter, variable coverage, and the hosted URL were validated in this repo.

### Product story (reviewer should see this, not Assistant)

- [x] Copy says: agent is the **category manager**; buyer creates an event and set-and-forget
- [x] Autopilot remains via MCP (`*_negotiation_agent*` → `/api/negotiation-agents`)
- [x] No user-facing push to `&assistant=1` / in-app Assistant panel

### Repo

- [x] Public GitHub repo: `https://github.com/supernegotiate/supernegotiate-cursor`
- [x] Submit that URL at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish)
- [x] Listing is free. Cursor reviews every listing and update manually.

## After listing

Buyers: Plugins → SuperNegotiate → paste `snk_` key → “create a fibre RFQ and babysit it until award”.

Self-host: override MCP URL; see [docs/mcp.md](mcp.md).
