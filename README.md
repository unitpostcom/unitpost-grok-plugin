# Unitpost for Grok

Your AI Agent sends the messages. [Unitpost](https://www.unitpost.com) is email
and SMS infrastructure, run by your agent: this plugin lets Grok send
transactional email and SMS, run campaigns, and manage contacts, templates,
sending domains, and webhooks in your Unitpost workspace.

## What this plugin installs

- One remote, Streamable HTTP MCP server:
  - Name: `unitpost`
  - Endpoint: `https://mcp.unitpost.com/mcp`
  - Tools: one per Unitpost API operation, for example `email_send`,
    `email_list`, `sms_send`, `contacts_create`, `email_campaigns_send`,
    `email_domains_list`, `webhooks_create`, and `docs_search`
- One skill, `skills/unitpost/SKILL.md`: when to use Unitpost, how its API
  works, and the send-safety rules the agent follows.

The plugin contains no executable scripts, hooks, downloaded binaries, or
embedded credentials.

## Authentication and access

The first connection uses Unitpost OAuth 2.1 with PKCE. Sign in to Unitpost,
pick a workspace, and approve the scopes. Grok handles the resulting OAuth
credentials; no API key is stored in this repository. Each authorization is
scoped to one workspace and to the approved scopes.

## Data and actions

Requests go to `mcp.unitpost.com` (MCP) and `www.unitpost.com` (`/oauth/*` and
`/.well-known/oauth-*` for sign-in). Unitpost reads and writes the data in the
selected workspace: contacts, templates, campaigns, domains, webhooks, and
message history.

Send tools send real messages, so they are marked destructive and Grok asks
before anything goes out. Email sends need a verified sending domain. SMS is on
paid plans and enforces recipient consent and STOP opt-out. Unitpost only sends
to people who opted in: no cold outreach or purchased lists.

## Links

- Docs: https://www.unitpost.com/docs/ai/grok
- Privacy: https://www.unitpost.com/privacy
- Terms: https://www.unitpost.com/terms
- Support: support@unitpost.com
