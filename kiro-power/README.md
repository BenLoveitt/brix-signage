# Brix Signage power for Kiro

Manage [Brix](https://brixsignage.com) digital signage from Kiro. Ask "which of my screens are offline, and why?" or "put the Summer Menu on every screen at Downtown for two hours", and Kiro does it through the hosted Brix MCP server.

This folder is a Kiro Power in the Agent Plugins format:

| File | What it does |
| --- | --- |
| `plugin.json` | The power manifest: name, version, description and the keywords that load the power |
| `mcp.json` | The `brix` server: `https://api.brixsignage.com/v1/mcp` (Streamable HTTP, OAuth) |
| `skills/brix-signage/SKILL.md` | How to work with Brix: the tools, the item kinds and the safe order to do things in |

Nothing runs on your machine. The power holds no code, binaries or credentials.

## Install

1. In Kiro, open **Powers**.
2. Click **Add Custom Power**, then **Import power from GitHub**.
3. Enter `https://github.com/BenLoveitt/brix-signage/tree/main/kiro-power`.
4. Ask something about your screens, for example "list my Brix screens". Kiro opens the Brix sign-in page in your browser the first time. Sign in, choose the workspace and what Kiro may do, and click **Allow**.

You need a Brix account. Every plan includes MCP ($6 per screen per month billed annually, $8 monthly). [Start a 7-day free trial](https://brixsignage.com/start), no card needed.

### Use an API key instead of OAuth

Kiro web and mobile sessions do not run OAuth. There, or for unattended runs, add a server with an API key to your Kiro MCP settings:

```json
{
  "mcpServers": {
    "brix": {
      "url": "https://api.brixsignage.com/v1/mcp",
      "headers": { "Authorization": "Bearer ${BRIX_API_KEY}" }
    }
  }
}
```

Make the key in the Brix console under **Settings > AI, API & MCP > API keys**. A view-only key is a safe first try: Kiro can answer questions but cannot change what plays.

## What Kiro can do

15 tools: list, read, create, update, delete and restore every kind of item (screens, media, playlists, schedules, layouts, creatives, apps, data sources, screen groups, locations, folders, emergency templates); put content on screens or start a takeover; check screen status, take a screenshot, refresh or reboot a screen; review approvals; and read uptime, proof of play and the audit log.

Every tool declares MCP annotations (read-only, destructive, idempotent), so Kiro knows when to ask first.

## Safety

- Kiro acts as you, inside the workspace, locations and permission areas you approve at sign-in. It cannot get a permission you do not hold.
- Deletes go to a 30-day recycle bin. `restore_items` brings them back.
- No access to billing, payment, API keys or single sign-on.
- Changes are recorded in the workspace audit log.
- Revoke the connection at any time in the Brix console under **Settings > AI, API & MCP > Connected apps**.

## Privacy

Brix receives only the tool calls, not your conversation. Brix does not train AI models on data accessed through connected apps. Read the [Brix privacy policy](https://brixsignage.com/privacy) and the [terms](https://brixsignage.com/terms).

## Support

- Email: [hello@brixsignage.com](mailto:hello@brixsignage.com)
- Docs: [brixsignage.com/developers/mcp](https://brixsignage.com/developers/mcp/)
- Security reports: [brixsignage.com/security](https://brixsignage.com/security/#report)

## License

MIT. The Brix service is covered by the [Brix terms](https://brixsignage.com/terms).
