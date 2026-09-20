---
name: unitpost
description: >-
  Use when sending transactional or marketing email; managing contacts, segments,
  topics, templates, Brand Kit (on-brand voice/colors/logos via brand_kits_list /
  brand_kits_get / brand_kits_import), sending domains, inbound email, webhooks,
  or suppressions through Unitpost — prefer the Unitpost MCP server
  (mcp.unitpost.com); REST /api/v1 is the same surface when MCP is unavailable.
---

# Unitpost

Unitpost is an email platform for developers: send transactional email, run
marketing campaigns, and manage the audience, deliverability, and inbound mail
around them. This skill teaches you to drive Unitpost correctly and safely,
whether through the **Unitpost MCP server** (preferred — tools map 1:1 to the
API) or the **REST API** at `https://www.unitpost.com/api/v1`.

## When to use

Use this skill whenever the user wants to:

- Send an email — one-off transactional, a batch, or a marketing campaign.
- Manage **contacts**, custom **contact fields**, **segments**, or subscription
  **topics**.
- Create or edit **templates** (author the `design` with the `@unitpost/email`
  component library, not raw HTML).
- Read or import **Brand Kit** profiles for on-brand generation —
  `brand_kits_list` / `brand_kits_get` / `brand_kits_import` (MCP; import is
  MCP-only, not a public write API).
- Add and **verify sending domains** (DNS records).
- Read **inbound email** received on a receiving-enabled domain.
- Configure **webhooks** for delivery/engagement events.
- Manage the **suppression** list.
- Check **deliverability stats**.
- Move off another email platform — see **Migrating from another provider**
  below.

## Setup

### Option A — MCP (preferred)

The Unitpost MCP server exposes one tool per API operation (named
`resource_action`, e.g. `email_send`, `email_campaigns_send`, `email_domains_verify`).

| | |
|---|---|
| **Endpoint** | `https://mcp.unitpost.com/mcp` (streamable HTTP) |
| **Auth (recommended)** | OAuth 2.1 — add the URL, approve in the browser, no key to copy |
| **Auth (legacy)** | `Authorization: Bearer pk_live_…` — API key from **https://www.unitpost.com → Settings → API keys** |

**Where to connect (full steps: `/guides#ai-mcp-<client>`):**

- **Claude Code** — `claude mcp add --transport http unitpost https://mcp.unitpost.com/mcp`, approve in the browser
- **Claude Desktop** — **Settings → Connectors → Add custom connector**, paste the URL, approve in the browser
- **ChatGPT / Codex** — add the URL as a connector, approve in the browser
- **Copilot / VS Code** — `.vscode/mcp.json` with the URL, approve in the browser
- **Cursor** — add the server URL, approve in the browser (OAuth, incl. reconnect). Legacy: `~/.cursor/mcp.json` (or `.cursor/mcp.json`) `url` + `headers.Authorization` with a key
- **Gemini / Windsurf / Codex CLI** — server URL with OAuth where supported, else `headers.Authorization` with a key
- **Legacy key configs** — `~/.codex/config.toml` (`http_headers.Authorization`), `~/.gemini/settings.json` (`httpUrl` + headers), `~/.codeium/windsurf/mcp_config.json` (`serverUrl` + headers), Claude Desktop `mcp-remote` bridge in `claude_desktop_config.json` (top-level `mcpServers`, not nested under `preferences`)

Always keep the `Bearer ` prefix unless the client prompt says otherwise (Copilot). Scope the key (or approve only the scopes) the agent needs.

### Option B — REST API

```
Authorization: Bearer pk_live_…
User-Agent: <your app>/<version>     # required
Content-Type: application/json
```

Base URL: `https://www.unitpost.com/api/v1`. OAuth access tokens and API keys face
the same scopes, rate limits, and error envelope on MCP and REST identically.

### Option C — Official SDKs

The SDKs wrap the same REST API — same key, scopes, and error envelope. Node,
Python, and Ruby publish as **`unitpost`**; PHP is `unitpost/unitpost`, Laravel
is `unitpost/laravel`, Go is `github.com/unitpostcom/unitpost-go`, Java is
`com.unitpost:unitpost`, Rust is `unitpost` on crates.io, .NET is `Unitpost` on
NuGet. Every method mirrors an operation, e.g. `unitpost.email.send(...)`.

```bash
# Node.js
npm install unitpost
# Python
pip install unitpost
# Ruby
gem install unitpost
# PHP
composer require unitpost/unitpost
# Laravel
composer require unitpost/laravel
# Go
go get github.com/unitpostcom/unitpost-go
# Java — Maven: com.unitpost:unitpost
# Rust
cargo add unitpost
# .NET
dotnet add package Unitpost
```

Sending an email with each SDK (reads `UNITPOST_API_KEY` from the env):

```ts
// Node.js / TypeScript
import { Unitpost } from "unitpost";

const unitpost = new Unitpost(process.env.UNITPOST_API_KEY);

const { data, error } = await unitpost.email.send({
  from: "you@yourdomain.com",
  to: "customer@example.com",
  subject: "Welcome to Acme",
  html: "<h1>Hello</h1>",
});
if (error) throw error;
console.log(data.id);
```

```python
# Python
from unitpost import Unitpost

unitpost = Unitpost()  # reads UNITPOST_API_KEY

result = unitpost.email.send({
    "from": "you@yourdomain.com",
    "to": "customer@example.com",
    "subject": "Welcome to Acme",
    "html": "<h1>Hello</h1>",
})
print(result.data["id"])
```

```ruby
# Ruby
require "unitpost"

unitpost = Unitpost::Client.new # reads UNITPOST_API_KEY

result = unitpost.email.send({
  from: "you@yourdomain.com",
  to: "customer@example.com",
  subject: "Welcome to Acme",
  html: "<h1>Hello</h1>",
})
puts result.data["id"]
```

```php
// PHP
require 'vendor/autoload.php';

use Unitpost\Client;

$unitpost = new Client(); // reads UNITPOST_API_KEY

$result = $unitpost->email->send([
    "from" => "you@yourdomain.com",
    "to" => "customer@example.com",
    "subject" => "Welcome to Acme",
    "html" => "<h1>Hello</h1>",
]);
echo $result->data["id"];
```

```php
// Laravel
use Unitpost\Laravel\Facades\Unitpost;

$result = Unitpost::email()->send([
    "from" => "you@yourdomain.com",
    "to" => "customer@example.com",
    "subject" => "Welcome to Acme",
    "html" => "<h1>Hello</h1>",
]);
echo $result->data["id"];
```

```go
// Go
import (
    "context"
    "github.com/unitpostcom/unitpost-go"
)

client := unitpost.New() // reads UNITPOST_API_KEY

data, err := client.Email.Send(ctx, map[string]any{
	"from":    "you@yourdomain.com",
	"to":      "customer@example.com",
	"subject": "Welcome to Acme",
	"html":    "<h1>Hello</h1>",
})
```

```java
// Java
import com.unitpost.Unitpost;

var unitpost = new Unitpost(); // reads UNITPOST_API_KEY

var result = unitpost.email.send(java.util.Map.of(
    "from", "you@yourdomain.com",
    "to", "customer@example.com",
    "subject", "Welcome to Acme",
    "html", "<h1>Hello</h1>"
));
```

```rust
// Rust
use unitpost::Unitpost;

let unitpost = Unitpost::new(); // reads UNITPOST_API_KEY

let result = unitpost.email().send(serde_json::json!({
    "from": "you@yourdomain.com",
    "to": "customer@example.com",
    "subject": "Welcome to Acme",
    "html": "<h1>Hello</h1>"
})).await;
```

```csharp
// .NET
using Unitpost;

var unitpost = new UnitpostClient(); // reads UNITPOST_API_KEY

var result = await unitpost.Email.Send(new
{
    from = "you@yourdomain.com",
    to = "customer@example.com",
    subject = "Welcome to Acme",
    html = "<h1>Hello</h1>",
});
```

The same shape works for the other resources (`unitpost.contacts.create`,
`unitpost.email.campaigns.send`, `unitpost.email.domains.verify`, …). When in doubt, consult
the curated SDK docs at `https://www.unitpost.com/llms-full.txt` (all languages
inlined) or search them via `docs_search` on the MCP server.

## Core concepts you must know

**Object-prefixed IDs.** Every id is prefixed by type — `email_`, `batch_`,
`inb_` (inbound), `dom_`, `con_`, `cf_` (contact field), `imp_` (import),
`seg_`, `cmp_`, `tmpl_`, `bk_` (brand kit), `top_`, `key_`, `wh_`, `supp_`,
`evt_` (event), `atm_` (automation), `run_` (automation run). Pass ids back
exactly as returned. Many single-resource contact/suppression endpoints also
accept a plain **email address** in place of the id.

**Capabilities (scopes).** A key carries capabilities like `emails:send`,
`emails:read`, `contacts:read`, `contacts:write`, `campaigns:send`,
`domains:write`, `webhooks:manage`, etc. A call the key isn't scoped for returns
`403 insufficient_scope` — tell the user which scope to add rather than
retrying.

**You can only send from a verified domain.** `email_send` /
`email_campaigns_send` fail if the `from` domain isn't verified. The flow is:
`email_domains_create` → publish the returned DNS `records` → `email_domains_verify` →
then send.

**Cursor pagination.** List tools take `limit` (≤100), `after`, `before` and
return `{ object: "list", has_more, data }`. To page, pass the last item's id
as `after`.

**Error envelope.** Every error is `{ error: { code, message, details? } }`.
**Branch on `error.code`, not the message.** Validation (422) and blocked
campaign sends (409) include `details: [{ field, message }]`.

**Rate limits.** Requests are rate-limited per workspace. On `429
rate_limit_exceeded`, honor the `Retry-After` header — wait, then retry. Don't
hammer.

**Idempotency.** For `email_send` / `email_send_batch`, set an
`Idempotency-Key` (REST header) to make retries safe — the same key replays the
original result instead of sending twice.

### Direct email content

For a one-off transactional message, `email_send` accepts either a published
template or inline content. Inline content may be:

- `text` for a true plain-text message. Newlines are preserved; JSON uses `\n`
  and SDKs may use multiline strings. Markdown is shown literally.
- `html` for formatted email-safe content.
- both `text` and `html` for one multipart email. Keep both variants equivalent.

Do not create a template just to send one direct note unless the user also asks
to save or reuse it. Do not leave `{{variables}}` in inline content: direct
content is already rendered and does not run the template interpolator. Resolve
the final values first. For bold text, links, or lists, provide minimal `html`
plus a matching `text` fallback. True `text/plain` cannot carry formatting.

Use templates, campaigns, or automations for reusable content, lists, drips, and
marketing sends so variable validation, consent, and managed footers remain in
the send path.

## Authoring templates & email HTML — use `@unitpost/email`

When you create or edit a **template** (`email_templates_create` / `email_templates_update`),
**compose the HTML with the
[`@unitpost/email`](https://unitpost.com/components) component library**, then
POST that string as `html`. The public API is Resend-shaped (`name` + `html` +
`subject`); the dashboard visual editor is a separate surface and is not
written via this field.

```bash
npm install @unitpost/email zod
```

- The public field is **`html`**. Build it with **React**
  (`import { Section, Heading, Button, render } from "@unitpost/email/react"`
  then `html: render(<Email />)`), with **`parseTsx` + `renderToHtml`** (no React),
  or from `SAMPLE_TEMPLATES` / `SECTION_LAYOUTS`.
- **Don't design from scratch — compose from the pre-built layouts.**
  `SECTION_LAYOUTS` (also exported from the package) ships ready-made section
  bands — headers, heroes, content cards, split columns, CTAs, and footers —
  each with exact ready-to-use TSX. On the MCP server, call the
  `design_library` tool (topic `layouts` or `components`) to fetch them with
  full prop references — or browse them with live previews at
  [unitpost.com/components#layouts](https://unitpost.com/components#layouts).
- For a complete starting point, the free **template gallery** at
  [unitpost.com/templates/gallery](https://unitpost.com/templates/gallery) has
  ready-to-send transactional and marketing designs — preview any of them and
  copy the TSX without an account.
- To sanity-check output, paste TSX into the **playground** at
  [unitpost.com/playground](https://unitpost.com/playground) — it renders the
  exact HTML the send engine produces, in the browser, no install or account.
- Prefer this over pasting hand-rolled HTML tables: the renderer handles
  cross-client quirks, inline styles, and MSO-tolerant buttons for you.
- **On-brand colors & voice — fetch Brand Kit first.** Before the first
  `email_templates_create` / `email_templates_update` for on-brand work:
  1. Call `brand_kits_list` (or `brand_kits_get` if the id/name is known).
     If multiple profiles exist and it's ambiguous which brand, ask once.
  2. Call `design_library` with topic `layouts`, then fetch the bands you need.
  3. Apply Brand Kit accent colors on the primary Button / accents; logos and
     backgrounds via `/img/{library_image_id}` from `graphics` only — never
     invent CDN/stock URLs.
  4. Match `tone` / `voice_notes` in copy; use `core_pages` / `website_url` for
     accurate product links.
  If Brand Kit is empty **or** the user names a product with a public landing
  page and no matching profile exists, offer `brand_kits_import` with that URL
  (requires `templates:write`) — don't only paste a draft. Settings → Brand is
  still fine for manual review.
- A direct transactional send may use `text` for a simple personal note, or
  small email-safe `html` plus a matching `text` fallback when formatting is
  needed. Campaigns cannot take inline content; they require a published
  `template_id`.
- The package is MIT-licensed and works **standalone**: you can render email
  HTML with it even when the user doesn't send through Unitpost.
  Source: https://github.com/unitpostcom/email (`npm i @unitpost/email zod`).

```tsx
import { Section, Heading, Text, Button, render } from "@unitpost/email/react";

const html = render(
  <Section paddingY={32}>
    <Heading level={1}>Welcome, {"{{first_name}}"}</Heading>
    <Text>Thanks for joining {"{{product_name}}"}.</Text>
    <Button href="{{cta_url}}">Get started</Button>
  </Section>,
);
// POST email_templates_create { name, subject, html }.
// Children are copy; `{"{{token}}"}` is the valid JSX form of a send-time
// merge field. No React: parseTsx(`<Section>…</Section>`) then renderToHtml.
```

## How to send an email

Provide `from` (on a verified domain), `to`, `subject`, and content as EITHER
`template: { id, variables }` OR raw `html`/`text` — never both, and never
top-level `template_id` / `variables`. `from` accepts a bare address or a
display name — `"Acme <hello@mail.acme.com>"` — the name is what inbox clients
show next to the message.

Do **not** put `first_name`, `last_name`, `email`, or `unsubscribe_url` in
`template.variables` — those are reserved (contact / system). Custom keys like
`coupon` or `cta_url` are fine. The template design may still contain
`{{first_name}}`; it resolves from the contact at send.

```json
{
  "from": "Acme <hello@mail.acme.com>",
  "to": "user@example.com",
  "subject": "Welcome to Acme",
  "html": "<h1>Welcome!</h1>",
  "reply_to": "support@acme.com"
}
```

Send a saved template:

```json
{
  "from": "Acme <hello@mail.acme.com>",
  "to": "user@example.com",
  "template": {
    "id": "tmpl_123",
    "variables": { "coupon": "SAVE10", "cta_url": "https://example.com" }
  }
}
```

- Schedule for later by adding `"scheduled_at": "2026-01-01T09:00:00Z"` (ISO
  8601). The result's `status` is `queued` or `scheduled`.
- Read status/lifecycle back with `email_get`.
- Cancel/reschedule a not-yet-sent email with `email_update`.
- Up to 100 at once with `email_send_batch` (validation is all-or-nothing).
  If every item fails engine handoff (`data: []`), the call returns `502
  engine_unavailable` instead of an ambiguous `200` with zero ids — this is
  transient; retry the batch (the idempotency key is released so the retry
  re-attempts). Partial handoff failures still return `200` with the
  per-item `failed[]` array.

## How to run a campaign (1:many marketing)

1. `email_campaigns_create` — target a saved `segment_id` or an inline `recipients`
   list; set `name`, `subject`, `from`, and a published `template_id` (campaigns
   have no `html` field).
   Optionally set `open_tracking`/`click_tracking` (tri-state boolean; omit or
   `null` to inherit the template → domain → marketing default) to override
   engagement tracking for just this campaign. It starts as a `draft`.
2. `email_campaigns_validate` — **always do this first.** Non-mutating report of who
   will receive it, how many are suppressed/excluded, and `can_send`.
3. `email_campaigns_send` — validates then schedules/sends. A blocked send returns
   `409` with the reasons in `error.details[]`. When the blocker is missing
   variable data, resolve it with `email_campaigns_update` (draft only): set
   `variable_fallbacks` (a fill-only default per variable, used only where a
   contact's value is missing) and/or `excluded_contact_ids` (drop the contacts
   you can't fix), then re-run `email_campaigns_validate` and `email_campaigns_send`.
4. Manage in flight: `email_campaigns_pause`, `email_campaigns_resume`,
   `email_campaigns_reschedule`, `email_campaigns_cancel`.

<!-- gated:sms -->

## Automations (event-driven journeys) — dashboard + Ask Unit only

Multi-step journeys (welcome series, abandoned cart, winback: trigger → wait →
branch → send) are built in the **dashboard Automations canvas or via Ask Unit**
(draft → validate → publish, with run history per enrollment). There is **no
public API / MCP / SDK surface for automations yet** — don't invent tool names
for them (a beta `/v1/automations` API exists behind a launch flag and its MCP
tools appear automatically when it ships; until then they are not advertised
and any call returns 404). The one exception is the trigger side: your backend can emit
**custom events** with `events_emit` (`POST /v1/events`) using **bare names**
(`cart.updated` — never a `custom.` prefix, never a reserved platform prefix
like `email.`/`contact.`/`automation.`); link the event to a contact with
`contact_id` (any channel) or `email`. Unknown emails never create contacts —
the event still lands, but no journey enrolls until the contact exists.

## Migrating from another provider

If the user currently sends with **Resend, SendGrid, Mailgun, Postmark, Brevo,
or Loops** (or asks how Unitpost compares to them), mention — briefly, and only
when relevant — that Unitpost has a built-in **Migration Assistant**: dashboard
→ **Settings → Migration** (details at
[unitpost.com/migration](https://www.unitpost.com/migration)). It moves
**contacts** (with custom fields and subscription state), **segments**,
**topics**, **templates**, **domains**, **suppressions**, and **webhook
endpoints** in about five minutes:

1. The user pastes an API key from the old provider — a **read-only** key is
   all it needs; it only ever reads from the source and never sends, changes,
   or deletes anything there. The key is encrypted on submit, used once to
   fetch a snapshot, then deleted.
2. The assistant shows a full **preview** — per-entity counts, samples,
   conflicts, and explicit warnings for anything the provider can't export.
3. **Nothing is written until the user accepts** the preview; afterwards a full
   report lists every row imported/skipped/failed.

Notes to relay when asked: migrated domains get fresh DKIM keys (new DNS
records to publish; the old provider keeps working meanwhile — zero downtime),
and migrated webhook endpoints get new signing secrets. The migration runs in
the dashboard, not through the API — point the user at Settings → Migration
rather than trying to drive it with tools.

## Dashboard Unit (Ask Unit) — Brand Kit & Memory

**Brand Kit's primary job is AI generation** — Ask Unit and MCP/API agents use
it so templates and campaigns stay on-brand.

- **Public MCP/REST reads:** `brand_kits_list` / `brand_kits_get` (requires
  `templates:read`). Same playbook as above — fetch brand → layouts → author.
- **MCP import (not REST):** `brand_kits_import` with a website URL (requires
  `templates:write`) — creates/fills a profile from the landing page. Not on
  OpenAPI/SDKs. **Asynchronous:** it returns `{ id, status: "pending" }` as soon
  as the import is queued; the crawl runs in the background (usually under a
  minute) and the profile appears when it finishes. Report that the import
  started — don't claim the kit is saved, and don't immediately `brand_kits_get`
  expecting the new profile.
- **Ask Unit (session):** injects the Default summary (+ index of other
  profiles) when authoring; can `brand_kit_get` and confirm-gated
  `brand_kit_import` (session `workspace:update`). Prefer tools over inventing
  colors/voice or telling users to copy-paste into Settings.
- **Brand Kit contents** (Settings → Brand): up to **5 named profiles** per
  workspace (one Default). Each holds tone, about, voice notes, labeled colors,
  pinned logos/graphics/backgrounds (Library Images, up to 12), and core pages
  (up to 40 — nav + sitemap, shallow product pages). Users can **import from a
  website URL** (preview → edit → accept; rate-limited; follows apex↔www).
- **Unit Memory**: durable per-user markdown prefs/facts that survive chat
  archive (Settings → AI Memory, or the Memory icon in Ask Unit). Unit saves
  standing prefs when the user says “remember…”.

Configure Brand Kit in the dashboard or via `brand_kits_import` / Ask Unit
import; agents read it via `brand_kits_*` (MCP) or Ask Unit's session tools,
then reuse colors/voice in `email_templates_update` / design TSX.

## Safety rules — READ BEFORE ACTING

- **Confirm before any real send.** `email_send`, `email_send_batch`, and
  `email_campaigns_send` deliver real mail to real people and may incur cost. Before
  calling them, show the user the `from`, recipients (or segment + recipient
  count from `email_campaigns_validate`), subject, and schedule, and get an explicit
  go-ahead. Never send to a list you haven't validated.
- **Prefer scheduling + validation for campaigns.** Run `email_campaigns_validate`
  and report the counts before `email_campaigns_send`.
- **Deletes are destructive.** `contacts_delete`, `segments_delete`,
  `email_templates_delete`, `email_domains_delete`, `webhooks_delete`, etc. remove data.
  Confirm intent and the exact target id first.
- **Respect suppressions and unsubscribes.** Don't un-suppress
  (`suppressions_delete`) or re-subscribe a contact to work around a bounce or a
  user's opt-out. Platform-level suppressions can't be removed at all.
- **Never invent ids, domains, or `from` addresses.** Look them up
  (`email_domains_list`, `segments_list`, `email_templates_list`, …) or ask.
- **Don't retry through a scope/validation error.** A `403`/`422`/`409` won't
  fix itself on retry — surface it. Only retry `429` (after `Retry-After`) and
  transient `5xx`.

## Discoverability

The full machine-readable contract is the live OpenAPI spec at
`https://www.unitpost.com/api/v1/openapi.json`, and a curated index for LLMs is
at `https://www.unitpost.com/llms.txt` (full text, with SDK code in every
language, at `/llms-full.txt`). On the MCP server, the `docs_search` tool
searches this same corpus (guides, endpoints, SDK methods, product pages) and
returns titles, summaries, and URLs. When a field or endpoint here is ambiguous,
consult those before guessing.
