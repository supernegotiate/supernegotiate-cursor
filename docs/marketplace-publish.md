# Cursor marketplace publish checklist

Submit SuperNegotiate at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish). Same listing is what Grok Bot loads under **Settings → Plugins**. Install must match Bird: **marketplace plugin → hosted MCP + skills → paste key → ready**. Not an in-app Assistant panel.

## Must be true before you submit

### Hosted MCP URL (no Mac tunnel)

- [ ] Production API serves Streamable HTTP MCP at **`https://api.supernegotiate.com/mcp`**
  - Railway API service (`railway-hikari`): root is **`/server`**. `server/railway.json` runs `node scripts/build-mcp.cjs` then `tsc`, then starts `node dist/server.js`. That compiles vendored `server/mcp/` (or repo-root `../mcp` when the image has it) into `server/mcp-dist`, which `attachMcpHttp` loads.
  - **After this lands on master, trigger a Railway redeploy of the API service.** Until that deploy finishes, `GET https://api.supernegotiate.com/mcp` is Express `Cannot GET /mcp` (404) — the new mount is not live yet.
  - Sidecar alternative: `MCP_UPSTREAM` to a `mcp/` service (`mcp/railway.json`).
- [ ] `GET https://api.supernegotiate.com/mcp` (no `Accept: text/event-stream`) returns `{ "status": "ok", "name": "supernegotiate-mcp", "mcp": "/mcp" }`
- [ ] `GET https://api.supernegotiate.com/api/health` is already green
- [ ] Plugin default `SUPERNEGOTIATE_MCP_URL` is that hosted URL (`plugins/supernegotiate/.cursor-plugin/plugin.json`)

### Manifest

- [ ] `plugins/supernegotiate/.cursor-plugin/plugin.json` — unique kebab-case `name`, description, version, logo, **variables** for `SUPERNEGOTIATE_API_KEY` (required) and `SUPERNEGOTIATE_MCP_URL` (optional, production default)
- [ ] `plugins/supernegotiate/plugin.json` — Agent Plugins manifest (Bird-shaped)
- [ ] `plugins/supernegotiate/mcp.json` — HTTP MCP with `${SUPERNEGOTIATE_MCP_URL}` and `Authorization: Bearer ${SUPERNEGOTIATE_API_KEY}`
- [ ] Repo-root `.cursor-plugin/marketplace.json` lists `source: plugins/supernegotiate`
- [ ] Every `${VAR}` in `mcp.json` is declared under `variables`
- [ ] Paths are relative (no `..`, no absolute paths)

### Logo, skills, README, privacy

- [ ] `plugins/supernegotiate/assets/logo.svg` committed and referenced as `assets/logo.svg`
- [ ] Skills with valid `SKILL.md` frontmatter: `category-manager`, `run-sourcing-event`, `bid-on-event`, `review-and-award`
- [ ] `plugins/supernegotiate/README.md` documents marketplace install (paste `snk_` key) — not localhost MCP
- [ ] `plugins/supernegotiate/PRIVACY.md` — what the key is used for, production hosts
- [ ] Plugin has been tested locally (symlink to `~/.cursor/plugins/local/supernegotiate`) against the hosted URL **or** a local `npm run mcp:http` override

### Product story (reviewer should see this, not Assistant)

- [ ] Copy says: agent is the **category manager**; buyer creates an event and set-and-forget
- [ ] Autopilot remains via MCP (`*_negotiation_agent*` → `/api/negotiation-agents`)
- [ ] No user-facing push to `&assistant=1` / in-app Assistant panel

### Repo

- [ ] Public GitHub repo: `https://github.com/supernegotiate/supernego2.0`
- [ ] Submit that URL at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish)
- [ ] Cursor reviews every listing and update manually

## After listing

Buyers: Plugins → SuperNegotiate → paste `snk_` key → “create a fibre RFQ and babysit it until award”.

Self-host: override MCP URL; see [docs/mcp.md](mcp.md).
