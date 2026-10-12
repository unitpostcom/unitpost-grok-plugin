# Installing the Unitpost MCP server

Unitpost is a hosted, remote MCP server. There is nothing to clone, build, or
run locally, and no API key to paste.

1. Add the server to the client's MCP settings as a remote Streamable HTTP
   server. For Cline (`cline_mcp_settings.json`, or the **Remote Servers** tab):

   ```json
   {
     "mcpServers": {
       "unitpost": {
         "type": "streamableHttp",
         "url": "https://mcp.unitpost.com/mcp",
         "disabled": false,
         "autoApprove": []
       }
     }
   }
   ```

2. Click **Authenticate**. A browser window opens Unitpost: sign in, pick a
   workspace, and approve the scopes. Tokens refresh automatically.
3. Verify by calling `email_domains_list`.

Notes for the agent doing the install:

- Leave `autoApprove` empty. Send tools (`email_send`, `sms_send`,
  `email_campaigns_send`, …) send real messages; the user should confirm each one.
- Email sends need a verified sending domain in the workspace. SMS is on paid
  plans only.
- Unitpost sends only to people who opted in: no cold outreach or purchased lists.
- Docs: https://www.unitpost.com/ai/mcp
