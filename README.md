# Unitpost for Grok

Official Unitpost plugin: agents send email (with approval), manage contacts,
campaigns, domains, webhooks.

## Contents

- `.mcp.json` — remote MCP server `https://mcp.unitpost.com/mcp` (streamable HTTP)
- `skills/unitpost/SKILL.md` — when to use Unitpost, safety rules (approval before send)
- `.plugin/plugin.json` — plugin manifest

## Auth

OAuth 2.1 in the browser (recommended). API-key Bearer is the backup for
paste-a-key clients. No credentials are baked into this plugin.

## Network endpoints called

- `https://mcp.unitpost.com/mcp` (MCP)
- `https://www.unitpost.com/oauth/*` (OAuth authorize/token)
- `https://www.unitpost.com/.well-known/oauth-*` (discovery)

## Links

- Docs: https://www.unitpost.com/ai/mcp
- Privacy: https://www.unitpost.com/privacy
- Terms: https://www.unitpost.com/terms
- Support: support@unitpost.com

## Canonical source + mirror

This directory is the canonical source. CI mirrors it to the standalone
public repo [`unitpostcom/unitpost-grok-plugin`](https://github.com/unitpostcom/unitpost-grok-plugin)
on every `main` push (`.github/workflows/grok-plugin-mirror.yml`), adding the
fresh `skills/unitpost/SKILL.md` and brand logos at mirror time — so the
skill can never drift from the API it documents. The xAI marketplace entry
pins that repo at a commit SHA (remote source).
