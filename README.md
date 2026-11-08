# Brix Signage MCP server

Control [Brix](https://brixsignage.com) digital signage screens from Claude, ChatGPT, Cursor or any MCP client. Ask "which of my screens are offline, and why?" or "put the Summer Menu on every screen at Downtown for two hours", and the agent does it through Brix's hosted Model Context Protocol server.

- **Endpoint:** `https://api.brixsignage.com/v1/mcp`
- **Transport:** Streamable HTTP (one JSON-RPC 2.0 message per `POST`, JSON reply, no SSE stream)
- **Auth:** OAuth 2.1 with PKCE (sign in, no key to copy), or `Authorization: Bearer <api key>`
- **Hosted by Brix:** nothing to install or run. This repo holds the docs and an agent skill. The server itself is closed source.
- **Registry:** `com.brixsignage/brix` in the official MCP Registry
- **Price:** included on every Brix plan ($6 per screen per month billed annually, $8 monthly). [7-day free trial](https://brixsignage.com/start), no card needed.

Full docs: [brixsignage.com/developers/mcp](https://brixsignage.com/developers/mcp/)

## What an agent can do

15 tools, the same for every client (an API key adds 2 for large uploads):

| Area | Tools |
| --- | --- |
| Every kind of item: screens, media, playlists, schedules, layouts, creatives, apps, data sources, screen groups, locations, folders, emergency templates | `list_items`, `get_items`, `create_items`, `update_items`, `delete_items`, `restore_items` |
| People and roles (see, and invite with a role that does not manage access) | `list_items`, `get_items`, `create_items` |
| Put content on screens | `set_screen_content` (lasting), `start_takeover`, `end_takeover` (for a while, or an emergency) |
| Find and fix problems | `get_screen_status`, `capture_screenshot`, `control_screens` (refresh, reboot, clear cache, TV on or off) |
| Approvals, data and reports | `review_approval`, `refresh_data`, `get_fleet_report` (uptime, proof of play, audit log) |
| Large files (API key only) | `mint_upload_url`, `confirm_media_upload` |

Every tool declares MCP annotations (read-only, destructive, idempotent, open-world), so clients know when to ask first.

## Safety

- The agent acts as you, inside the workspace, location and permission areas you approve. It cannot get a permission you do not hold.
- Deletes go to a 30-day recycle bin and come back with `restore_items`. A permanent delete needs two calls and a confirmation code.
- No access to billing, payment, API keys or single sign-on, and no changes to a person's access.
- Content created over MCP lands as a draft where the location requires approval. Changes are recorded in the workspace audit log.
- Brix does not receive your conversation, only the tool calls, and does not train AI models on data accessed through connected apps.
- Revoke any connection at once in the Brix console under **Settings > AI, API & MCP > Connected apps**.

## Connect

### Claude (claude.ai, Desktop, mobile)

Brix is in [Claude's connector directory](https://claude.ai/directory/connectors/brix). Go to **Customize > Connectors**, search for Brix, click **Connect**, sign in and choose what Claude may do.

### ChatGPT

Turn on Developer mode under **Settings > Security and login**, then add `https://api.brixsignage.com/v1/mcp` at [chatgpt.com/plugins](https://chatgpt.com/plugins). Sign in when Brix asks.

### Claude Code

```bash
claude mcp add --transport http brix https://api.brixsignage.com/v1/mcp \
  --header "Authorization: Bearer $BRIX_API_KEY"
```

### Cursor, LobeHub, and other clients with remote MCP

```json
{
  "mcpServers": {
    "brix": {
      "url": "https://api.brixsignage.com/v1/mcp",
      "headers": { "Authorization": "Bearer <YOUR_API_KEY>" }
    }
  }
}
```

Clients that support MCP OAuth can leave out `headers` and sign in instead.

### Claude Desktop with an API key (stdio bridge)

```json
{
  "mcpServers": {
    "brix": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://api.brixsignage.com/v1/mcp", "--header", "Authorization:${BRIX_AUTH}"],
      "env": { "BRIX_AUTH": "Bearer ak_us_live_..." }
    }
  }
}
```

Create an API key in the Brix console under **Settings > AI, API & MCP > API keys**. For a first try, a view-only key is safe: the agent can answer questions but cannot change what plays.

## Agent skill

`skills/brix-signage/SKILL.md` teaches a coding agent how to work with Brix: the MCP tools, the `brix` CLI and the REST API, and the safe order to do things in. Install it with:

```bash
npx skills add BenLoveitt/brix-signage
```

## Things to ask

- "Which of my screens are offline, and why?"
- "Why is the lobby screen blank? Show me what it's displaying right now."
- "Put the Summer Menu playlist on every screen at the Downtown location for the next two hours."
- "How many times did the Burger Promo play last week, and on which screens?"
- "Give me this month's uptime report, worst screens first."

## Test with curl

```bash
curl https://api.brixsignage.com/v1/mcp \
  -H "Authorization: Bearer $BRIX_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

## Also from Brix

- [REST API](https://brixsignage.com/developers/api/) and [OpenAPI spec](https://api.brixsignage.com/v1/openapi.json)
- [`brix` CLI](https://brixsignage.com/developers/cli/): `npm install -g brixsignage`
- [Brix](https://brixsignage.com/): the CMS, player apps and the Brix Signage Stick

## Support

Email [hello@brixsignage.com](mailto:hello@brixsignage.com). Security reports: [brixsignage.com/security](https://brixsignage.com/security/#report).

## License

The docs and skill in this repo are MIT licensed. The Brix service is covered by the [Brix terms](https://brixsignage.com/terms).
