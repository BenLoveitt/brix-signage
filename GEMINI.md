# Brix digital signage (Gemini CLI extension)

The `brix` MCP server (`https://api.brixsignage.com/v1/mcp`) controls the user's Brix digital signage screens. Its tools act on real screens in a real workspace.

- If the `brix` tools return an authentication error, tell the user to run `/mcp auth brix` and sign in in the browser. Never ask the user to paste an API key or password into the chat.
- In an unfamiliar workspace, call `get_items` with kind `account` first: it returns the workspace and what this sign-in may do.
- Find ids with `list_items` (by name) and read items with `get_items`. Never guess an id.
- Before a change that shows on screens (`set_screen_content`, `start_takeover`, `control_screens`, `delete_items`), say what will change and on which screens, then ask the user to confirm.
- Deletes go to a 30-day recycle bin; `restore_items` brings them back.

The bundled `brix-signage` skill has the full guide (tools, kinds, the `brix` CLI and the REST API).
