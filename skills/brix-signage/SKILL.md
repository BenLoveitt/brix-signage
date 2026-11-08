---
name: brix-signage
description: Manage Brix digital signage screens, playlists, schedules, media and emergency alerts through the Brix MCP server, the brix CLI or the Brix REST API. Use when the user asks about their Brix screens (offline, blank, what is playing), wants to put content on screens, upload media, build playlists or schedules, send an emergency message, read proof of play or uptime, or connect Brix to an agent.
license: MIT
---

# Brix digital signage

Brix (brixsignage.com) runs digital signage screens: TVs, Brix Signage Sticks and other players, managed from a web console. You can work with it three ways. Pick the first one that is available.

1. **MCP server** (best): `https://api.brixsignage.com/v1/mcp`. If tools named `list_items`, `get_items`, `get_screen_status` and so on are already available, use them.
2. **CLI**: `npx brixsignage <command>` (or `npm install -g brixsignage`, then `brix`). Needs `BRIX_API_KEY` or `brix login`.
3. **REST API**: `https://api.brixsignage.com/v1/...` with `Authorization: Bearer $BRIX_API_KEY`. The spec is at `https://api.brixsignage.com/v1/openapi.json`.

Never ask the user to paste an API key into the chat. Ask them to set `BRIX_API_KEY` in their environment, or to connect by OAuth.

## Connecting

- **Claude Code:** `claude mcp add --transport http brix https://api.brixsignage.com/v1/mcp --header "Authorization: Bearer $BRIX_API_KEY"`
- **claude.ai / Claude Desktop / mobile:** Customize > Connectors, search Brix, Connect, sign in.
- **Cursor and other remote-MCP clients:** `{"mcpServers":{"brix":{"url":"https://api.brixsignage.com/v1/mcp","headers":{"Authorization":"Bearer ${env:BRIX_API_KEY}"}}}}`
- **API keys** are made in the Brix console: Settings > AI, API & MCP > API keys. Suggest a view-only key first.

## How to work (MCP)

1. In an unfamiliar workspace, start with `get_items` kind `account`: it returns the workspace and what the key may do.
2. Find ids with `list_items` (by name) and read items in full with `get_items`. Never guess an id.
3. Kinds are shared by every tool: `screens`, `media`, `playlists`, `schedules`, `layouts`, `creatives`, `apps`, `data_sources`, `screen_groups`, `locations`, `media_folders`, `emergency_templates`, plus `people`, `roles` and `alerts` for reading.
4. Change things with `create_items`, `update_items`, `delete_items` (to a 30-day recycle bin) and `restore_items`.
5. What a screen plays by default: `set_screen_content`. For a while, or an emergency: `start_takeover`, then `end_takeover`. To show a new announcement, make it first with `create_items` kind `creatives`, then take over with its id.
6. One screen: `get_screen_status` (why it shows what it shows, outages, TV state), `capture_screenshot`, `control_screens` (refresh, reboot, clear cache, TV on/off). The fleet: `get_fleet_report` (uptime, proof of play, audit log) and `list_items` kind `alerts`.
7. Approvals: `review_approval`. Data feeds: `refresh_data`.

## Rules

- Confirm with the user before deletes, emergencies, reboots and anything sent to many screens at once.
- A permanent delete takes two calls: the first returns a code and what would be erased; only send the second after the user says yes.
- Tools never take sign-in values. A data feed that needs a password or key (POS, Stripe, Shopify, a private sheet) is connected by a person in the console under Content > Data sources; then use it by id.
- Billing, payment, API keys, SSO and changing a person's access are done by a person in the console. Say so instead of trying.
- Where a location requires approval, new content lands as a draft. Tell the user it is waiting for approval.

## CLI quick reference

```bash
brix whoami
brix screens --search lobby
brix screens diagnose <screen_id>
brix screens screenshot <screen_id>
brix screens reboot <screen_id>
brix media upload ./menu.mp4 --name "Spring menu"
brix playlists
brix cast playlist <playlist_id> --screens <scr_a,scr_b> --for 30m
brix casts end <cast_id>
brix call <operationId> --param key=value   # any OpenAPI operation
```

## Docs

- MCP: https://brixsignage.com/developers/mcp/
- REST API: https://brixsignage.com/developers/api/
- CLI: https://brixsignage.com/developers/cli/
- Machine-readable index: https://brixsignage.com/llms.txt
