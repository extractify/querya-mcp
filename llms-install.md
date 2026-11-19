# Installing Querya (for AI agents)

Querya is a **remote** MCP server. There is nothing to clone, build or run, and no API key.

1. Add a remote MCP server named `querya`:
   - URL: `https://mcp.querya.io/mcp`
   - Transport: Streamable HTTP
   - Authentication: OAuth (the client registers itself; no client ID or secret is needed)

   Cline: add it to Cline's own MCP settings file, the one that opens from Cline → MCP Servers → Configure → "Configure MCP Servers". In Cline 4.x that file is `~/.cline/data/settings/cline_mcp_settings.json` (on Windows `%USERPROFILE%\.cline\data\settings\cline_mcp_settings.json`). Do not create a new file elsewhere (for example under `.vscode`): Cline only reads its own file. Keep any servers already in it and add `querya` to `mcpServers`:

   ```json
   {
     "mcpServers": {
       "querya": {
         "type": "streamableHttp",
         "url": "https://mcp.querya.io/mcp"
       }
     }
   }
   ```

2. On the first tool call the client opens the Querya sign-in in the browser. The user signs in with Google and chooses which Google Ads accounts Querya may reach.
3. Check the installation: the `querya` server shows as connected in Cline's MCP Servers panel; then call its `list_accounts` tool.

Notes for the agent:

- The user needs a Google account with access to at least one Google Ads account.
- The free plan is read-only. Write tools answer `PLAN_REQUIRED` until the user is on Pro or Max.
- Write tools never change the account directly: they return a preview with a `plan_id`. Call `confirm_change_plan` only after the user explicitly confirms.
