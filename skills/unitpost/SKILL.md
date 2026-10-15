---
name: unitpost
description: >-
  Use when sending transactional or marketing email;
  sending SMS text messages (consent, senders, country reach, sms_send);
  managing contacts, segments, topics, templates, Brand Kit (on-brand
  voice/colors/logos via brand_kits_list / brand_kits_get / brand_kits_import),
  sending domains, inbound email, webhooks, or suppressions through Unitpost.
  Prefer the Unitpost MCP server (mcp.unitpost.com); REST /api/v1 is the same
  surface when MCP is unavailable.
---

# Unitpost

Unitpost is an email platform for developers: send transactional email, run
marketing campaigns, and manage the audience, deliverability, and inbound mail
around them.
On paid plans it also sends SMS through the same API key, contacts, and webhooks.
This skill teaches you to drive Unitpost correctly and safely,
whether through the **Unitpost MCP server** (preferred — tools map 1:1 to the
API) or the **REST API** at `https://www.unitpost.com/api/v1`.

## When to use

Use this skill whenever the user wants to:

- Send an email — one-off transactional, a batch, or a marketing campaign.
- Send an **SMS** (paid plans): a transactional or marketing text, with consent, senders, and country reach. See **SMS** below.
- Manage **contacts**, custom **contact fields**, **segments**, or subscription
  **topics**.
- Create or edit **templates** — a **designed** email as `html` built with the
  `@unitpost/email` component library, or a **simple** (Gmail-style) email as
  `text`. Every template read carries its `kind`; keep it when you edit.
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

**Grok** — the Unitpost plugin connects this server for you. On first use, Grok opens Unitpost in the browser: sign in, pick a workspace, and approve. Full steps: `https://www.unitpost.com/docs/ai/grok`.

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

### Option D — CLI

`unitpost-cli` puts the same API in a shell, which is the right tool when the
user is working in a terminal, writing a shell script, or wiring a CI step —
and when you want to **show the user a command they can re-run themselves**.

```bash
brew install unitpostcom/tap/unitpost               # standalone binary, no Node.js
curl -fsSL https://www.unitpost.com/install.sh | sh  # same, without Homebrew (Windows: irm https://www.unitpost.com/install.ps1 | iex)
npm install -g unitpost-cli                          # or npm, with Node 20+ (npx unitpost-cli@latest for a one-off)

unitpost login                   # browser OAuth (same AS as MCP)
unitpost login --key pk_live_…   # or store an API key instead
unitpost whoami                  # which credential is in use, and what it can do
```

In CI, skip `login` entirely and set `UNITPOST_API_KEY` in the environment.

Every API operation is a command, named the same way the SDK methods and MCP
tools are, because all three are generated from the same spec:

```bash
unitpost email send \
  --from you@yourdomain.com \
  --to customer@example.com \
  --subject "Welcome to Acme" \
  --html "<h1>Hello</h1>"

unitpost contacts create --email customer@example.com --first-name Ada
unitpost email domains verify dom_123
```

**See [Driving the CLI](#driving-the-cli) below** for the full command map,
the convenience flags, scripting/CI rules, exit codes, and profiles.

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

**Custom-field defaults.** A contact field's `default_value` is the value used
for any contact that has none: when an email is sent (merge tags) and when
segments are evaluated. It's never written onto contacts, so changing it takes
effect everywhere at once. In segment filters, `is_set` / `is_not_set` check
whether the contact itself has a value.

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

### Simple emails — `text` instead of `html`

A template is one of two **kinds**, and every template read
(`email_templates_get` / `_list`) says which in `kind`:

- **`designed`** — newsletters, promos, product emails: `html`, composed with
  `@unitpost/email` as above (the dashboard's Design / Code editor).
- **`simple`** — a plain, personal email, "as if I typed it in Gmail", no
  layout and no brand band (the dashboard's Simple editor).

For a simple email pass **`text`** instead of `html` to `email_templates_create`
/ `email_templates_update` (exactly one of the two). **When you update a
template, keep its kind**: `text` for a `simple` template, `html` for a
`designed` one. Sending the other field converts it (html → designed code
mode, text → simple), so only do that when the user asks to switch.

`text` is what a person types into a compose box, and it can carry everything
the Simple editor can:

- one line per line; a **blank line** between paragraphs — blank lines are the
  only spacing;
- `- ` (or `* `) bullets and `1. ` numbers for lists, one item per line;
- inline `<b>`, `<i>`, `<u>`, `<s>`, `<a href="…">`, and `<br>` for a line
  break inside a line;
- colour, highlight, size and font with one span:
  `<span style="color:#cc0000; background-color:#fff3a0; font-size:18px; font-family:Georgia">…</span>`
  — sizes snap to Gmail's Small 13px / Normal / Large 18px / Huge 24px; fonts
  are Arial, Helvetica, Verdana, Tahoma, Trebuchet MS, Georgia, Times New
  Roman, Courier New. A title is `<b><span style="font-size:24px">…</span></b>`
  on its own line;
- alignment: wrap one whole line in `<div style="text-align:center">…</div>`
  (or `right`);
- an image alone on its own line: `<img src="/img/{id}" alt="…" width="400">`
  (optionally inside `<a href>`); `src` is a library `/img/{id}` or an
  `https://` URL — never invent one;
- `{{first_name}}`-style variables anywhere.

**Not supported** — removed when the template is saved, so don't send them:
headings (`<h1>`…), layout (sections, columns, spacers, buttons, dividers),
`@unitpost/email` components, `<p>` / `<div>` / `<table>` / `<ul>`, any other
CSS, and Markdown — `**bold**` and `[link](url)` stay literal, exactly as they
would in Gmail, Outlook or Spark.

It sends as a bare mail-client fragment (`<div dir="ltr"><div>…</div></div>`):
no doctype, tables, widths or background. Marketing templates end with one
plain footer line (sender · Unsubscribe · Sent with Unitpost) added at send
time — never write it yourself. Max 20,000 characters. Use `html` (and
`@unitpost/email`) for anything designed.

## How to send an email

Provide `from` (on a verified domain), `to`, `subject`, and content as EITHER
`template: { id, variables }` OR raw `html`/`text` — never both, and never
top-level `template_id` / `variables`. `from` accepts a bare address or a
display name — `"Acme <hello@mail.acme.com>"` — the name is what inbox clients
show next to the message.

Do **not** put `first_name`, `last_name`, `email`, or `unsubscribe_url` in
`template.variables` — those are reserved (contact / system). Custom keys like
`coupon` or `cta_url` are fine. The template design may still contain
`{{first_name}}`; it resolves from the contact at send. On a template send the
`from` display name can use variables too (`"{{company_name}} Team
<hi@mail.acme.com>"`); a raw `html`/`text` send fills no variables and refuses
a `{{…}}` in its `from` name.

A **marketing** template goes to exactly one recipient (no cc/bcc), and that
recipient becomes a **contact** if they aren't one yet — the unsubscribe link
belongs to a contact. Tell the user when a send will add someone to their
contacts. On the Free plan it counts toward the contact limit (at the limit the
send is refused); an address that already unsubscribed is added as
unsubscribed and the email is suppressed. Transactional templates never create
contacts. To send marketing to many people, use a campaign.

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

## SMS

Unitpost also sends SMS, on paid plans. The same key, scopes, error envelope,
pagination, and idempotency rules apply; the tools are `sms_send`, `sms_get`, `sms_list`,
`sms_stats`, `sms_brands_list`, `sms_numbers_list`, plus the contact pair `sms_consent_get`
/ `sms_consent_record`. REST: `POST /sms`, `GET /sms/{id}`, `GET /sms`,
`GET /sms/stats`, `GET /sms/brands`, `GET /sms/numbers`, `GET|POST /contacts/{id}/sms-consent`.
`sms_stats` is the SMS twin of `email_stats`: queued, sent, delivered, failed,
suppressed (skipped on purpose), canceled, inbound and segments billed over
`days` (default 30), with delivery, failure and suppression rates. There are
no open or click numbers on SMS.
In the SDKs the resource is `sms` (`unitpost.sms.send(...)`).

**SMS templates and topics** mirror their email counterparts under the SMS
channel, with the same scopes (`templates:*`, `topics:*`): `sms_templates_list`
/ `sms_templates_create` / `sms_templates_get` / `sms_templates_update` /
`sms_templates_delete` (`/sms/templates`, ids `tmpl_`) and `sms_topics_list` /
`sms_topics_create` / `sms_topics_get` / `sms_topics_update` /
`sms_topics_delete` (`/sms/topics`, ids `top_`). An SMS template is a
plain-text `body` with `{{variables}}`, created as a `draft`; only `published`
templates can be sent by an SMS campaign, and publishing needs a non-blank
`body` (`422` otherwise). Topics carry `channel` (`"email"` or `"sms"`), also
on a contact's topic subscriptions. SMS topics are separate from email
topics: an email opt-out never changes an SMS preference, and STOP still opts
a number out of every SMS. An email template or topic id returns `404` on the
SMS tools (and the reverse). In the SDKs: `unitpost.sms.templates.*`,
`unitpost.sms.topics.*`.

**SMS campaigns** send a published SMS template to a segment
(`/sms/campaigns`, ids `cmp_`), with the email campaign scopes:
`sms_campaigns_list` / `sms_campaigns_get` / `sms_campaigns_validate` need
`campaigns:read`, `sms_campaigns_create` / `sms_campaigns_update` /
`sms_campaigns_delete` need `campaigns:write`, and `sms_campaigns_send` /
`sms_campaigns_cancel` / `sms_campaigns_pause` / `sms_campaigns_resume` /
`sms_campaigns_reschedule` need `campaigns:send`. Create makes a `draft`
(omit `sender_id` for automatic routing; `snum_…` pins one sender;
`scheduled_at` saves a send time); edits are draft-only (`409` after, and
while archived). Before sending, run `sms_campaigns_validate`: it returns
`can_send`, `blockers`, `reachable`, `no_marketing_consent` and
`quiet_hours_deferred`, without sending. `sms_campaigns_send` runs the same
checks as the dashboard: with no `scheduled_at` it uses the draft's saved
send time if that is still ahead (otherwise sends now), `null` sends now, a
time schedules it. A blocked send is `409` with each reason in
`error.details[]` (`field` = what to fix, `code` = why). Delete archives
(`409` while a campaign is in flight: cancel first); `sms_campaigns_update`
with `{ archived: false }` restores, and `sms_campaigns_list` with
`archived=true` lists archived ones. In the SDKs:
`unitpost.sms.campaigns.*`.

**Paid plans only.** SMS is included on every paid plan and not on the free
plan. On a free workspace `sms_send` (`POST /sms`) and the campaign
send-class tools (`sms_campaigns_create`, `sms_campaigns_send`,
`sms_campaigns_resume`, `sms_campaigns_reschedule`) return `403` with
`error.code` `limit_reached`; the reads (`sms_get`, `sms_list`, `sms_stats`,
`sms_brands_list`, `sms_numbers_list`, `sms_campaigns_list`,
`sms_campaigns_get`, `sms_campaigns_validate`) keep working. A workspace whose paid
plan just ended has a 7-day grace window before that `403`; sends in it draw
purchased credit only, so without any they get `429` `quota_exceeded`.
Tell the user in one sentence that SMS needs a paid plan and point them at
Settings, Billing. Do not retry and do not look for a workaround.

**Ids.** SMS messages are `sms_`, SMS brands are `sbrand_`, phone numbers and
Sender IDs are `snum_`. Contacts keep their `con_` id; a contact's phone
number is a handle on the contact, not a separate object.

**In the CLI** the same operations are seven commands (see
[Driving the CLI](#driving-the-cli) for the shared rules on output, exit codes
and confirmation):

```bash
unitpost sms send --to +14155550123 --body "Your code is 123456"
unitpost sms get sms_123
unitpost sms list --limit 20
unitpost sms brands                       # brand status + what is still missing
unitpost sms numbers                      # sending identities and their reach
unitpost sms consent get con_456          # a contact's consent record
unitpost sms consent record con_456 --action opt_in --source web_form --purpose marketing
```

**Scopes.** `sms:read` reads messages, brands, and numbers; `sms:send` sends;
`sms:manage` and `sms:purchase` are dashboard-only (sender setup and paid
carrier submissions never happen from an agent). Consent reads and writes use
the contact scopes (`contacts:read` / `contacts:write`) because consent is
contact data.

### What is different from email

- **Consent is opt-in.** A marketing SMS needs the recipient's prior express
  consent on record. Email is opt-out by default; SMS is the reverse. A send
  without consent is skipped as `no_marketing_consent`, never attempted.
  Transactional SMS (receipts, alerts, verification codes) does not need
  marketing consent.
- **The SMS Messaging Policy applies to every send.** Consent and proof of
  consent, sender identification and opt-out instructions, quiet hours,
  prohibited content (SHAFT, cannabis, high-risk financial, lead generation,
  fraud) and registration truthfulness are set out at
  https://unitpost.com/sms-messaging-policy, which is part of the Acceptable
  Use Policy. When a send is refused or held, that page says why.
- **STOP, HELP, START are handled for you.** STOP opts the number out of the
  SMS channel entirely and it never touches the contact's email subscription.
  HELP returns the program help text. START re-opts the number in. Do not
  build keyword handling yourself.
- **Never record consent on the customer's behalf.** `sms_consent_record` is
  for consent the recipient actually gave (their signup form, a keyword, the
  hosted preference page), with `source` and, when backdated, `occurred_at`.
  If the user asks you to mark a list as opted in, decline and point at a
  real opt-in flow.
- **Quiet hours.** Marketing SMS respects recipient-local quiet hours. A
  message queued at midnight is deferred to the morning window: delayed, not
  dropped. Its skip reason is `quiet_hours_deferred` and there is nothing to
  fix.
- **Billing is per segment**, from the same monthly sending allowance (the
  wallet) that email draws, at the published rate for the recipient's
  country. There is one balance, not a separate SMS credit. 160 GSM-7
  characters fit one segment (153 each when a message spans several); Unicode
  fits 70/67. A skipped recipient is never billed. Number leases and carrier
  registration fees are also taken from the wallet, quoted and confirmed in
  the dashboard first; they are never a separate card charge. Sender IDs are
  free.
- **No opens or clicks.** Carriers report `sent`, `delivered`, and `failed`
  only. There is no open rate, click rate, or engagement tracking to request.
- **Brand registration comes first.** Before any production number or Sender
  ID the workspace needs an SMS brand (legal entity, website, contact email on
  that domain) that carriers or regulators approve. `sms_brands_list` returns
  each brand's `status` and a `missing` list saying exactly what is still
  required. Creating a brand and requesting numbers are paid submissions done
  in the dashboard.

### Reach: numbers and Sender IDs

Every sending identity delivers to specific countries. `sms_numbers_list`
returns each identity's `reach`.

- A US toll-free or 10-digit local (10DLC) number reaches US recipients only.
- A **Canadian local number** (`type: "long_code"`, `country: "CA"`) reaches
  Canadian recipients only. It is instant and needs no carrier registration
  (a small monthly fee). Sending to both the US and Canada needs both
  identities; a US toll-free never reaches Canada.
- An alphanumeric **Sender ID** shows the brand name on the handset instead of
  a number. It is free and enabled per destination country: instant in most
  of Europe, Latin America, Asia-Pacific, and Africa; requires regulator
  registration in some countries (United Kingdom, Spain, Singapore, and
  others), where approval takes days to weeks; not available in the United
  States, Canada, or China.
- Sender IDs are one-way. Recipients cannot text STOP to one, so marketing
  sent from a Sender ID must carry a link to the hosted preference page, and
  opt-outs are recorded through `sms_consent_record`.
- A recipient outside every sender's reach is skipped as `out_of_reach`, with
  a remedy naming the country ("Request a sender for United Kingdom",
  "Request a Canadian local number").

### Skip reasons: one vocabulary

Every surface that says a recipient was skipped uses the same word: campaign
preflight, the message `error.code` on `GET /sms/{id}`, the activity stream,
and the `sms.suppressed` webhook `reason`.

| Code | Meaning | What fixes it |
|---|---|---|
| `out_of_reach` | No sender on the message or campaign covers the recipient's country | Request a sender for that country |
| `unsupported_country` | Inside reach, but Unitpost has no compliance rule for the country yet | Transactional still sends; marketing rules for that country are on the roadmap |
| `no_phone` | The contact has no SMS number | Add a phone number to the contact |
| `opted_out` | STOP, the suppression list, or the number is unreachable | The recipient re-opts in by texting START or from the preference page |
| `no_marketing_consent` | Marketing without express consent on record | Record consent the recipient actually gave |
| `quiet_hours_deferred` | Held for the recipient's local window (delayed, not dropped) | Nothing; it sends when the window opens |
| `sender_not_active` | The sender lost approval or was released | Fix or replace the sender, then resume |

### Safety rules for SMS

- `sms_send` delivers a real message to a real phone and costs money. Show
  the user the sender, the recipient, and the body, and get an explicit
  go-ahead first. Never send to a list you have not checked for consent.
- `sms_campaigns_send` texts a whole segment and costs money. Run
  `sms_campaigns_validate` first, show the user the reachable count, the
  template body and the schedule (including a saved `scheduled_at`), and get
  an explicit go-ahead.
- The recipient must be E.164 (`+14155550123`).
- A `403` with `limit_reached` from `sms_send` or an `sms_campaigns_*`
  create/send/resume/reschedule means the plan does not include SMS (see
  **Paid plans only** above). Say so; do not retry.
- A `404` from every `sms_*` tool means SMS is switched off for the
  deployment. Say SMS is not available right now; do not retry.

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

## Driving the CLI

Everything below is the `unitpost` binary from [Option D](#option-d--cli). Read
this before you write a command you haven't verified with `--help`.

### One naming rule makes the whole surface guessable

Every command is exactly one `/api/v1` operation, nested the same way the SDK
methods and MCP tools are, because all three are generated from the same
OpenAPI contract. So these three are the same thing:

| Surface | Form |
|---|---|
| MCP tool | `email_templates_create` |
| SDK method | `unitpost.email.templates.create(...)` |
| CLI command | `unitpost email templates create` |

The practical consequence: if you know the MCP tool name, you know the command
— replace the underscores with spaces. A new endpoint becomes a new command
with no CLI release, so the tree below is what the API has today.

### Command map (75 commands over 8 resources)

Verbs are listed bare; prefix each with its resource, e.g. "campaigns send"
under email means `unitpost email campaigns send`.

| Resource | n | Commands |
|---|---|---|
| email | 38 | send, batch, get, list, update, stats; batches (cancel, get); campaigns (create, get, list, update, delete, validate, send, pause, resume, reschedule, cancel); domains (create, get, list, update, delete, verify); templates (create, get, list, update, delete); topics (create, get, list, update, delete); received (list, get, attachment-url) |
| contacts | 10 | create, get, list, update, delete; imports (create, get, list); topics (list, set) |
| segments | 8 | create, get, list, update, delete; members (add, list, remove) |
| contact-fields | 6 | create, get, list, update, delete, rename |
| webhooks | 6 | create, get, list, update, delete, test |
| suppressions | 4 | create, get, list, delete |
| brand-kits | 2 | get, list |
| usage | 1 | get |

Plus the CLI's own utilities (these are about the CLI, not the API):
`unitpost login`, `logout`, `whoami`, `doctor`, `open`, `completion`, and
`unitpost auth list|switch|rename|remove`.

Path parameters are positional, flags carry the body and query:

```bash
unitpost email campaigns validate cmp_123
unitpost segments members add seg_vip --contact con_456
unitpost contacts list --limit 50
```

### Convenience flags

A few operations are unusable as pure flag mappings — you can't paste an HTML
document or a base64 attachment into a shell flag. These overlays rewrite into
flags that already exist on the operation; they never invent a request shape.

| Flag | Available on | Becomes |
|---|---|---|
| `--html-file <path>` | email send, email templates create/update | the HTML body (`-` reads stdin) |
| `--text-file <path>` | email send, email templates create/update | the plain-text body (`-` reads stdin) |
| `--attach <path>` | email send | an attachments entry (filename + base64); repeatable |
| `--template-id <id>` | email send | the template object's id |
| `--var name=value` | email send | a template variable; repeatable |
| `--file <path>` | email batch, contacts imports create | the array/payload the operation expects |

```bash
unitpost email send --from you@yourdomain.com --to customer@example.com \
  --subject "Your invoice" --html-file ./invoice.html --attach ./invoice.pdf

unitpost email send --from you@yourdomain.com --to ada@example.com \
  --template-id tmpl_123 --var first_name=Ada --var plan=Pro

unitpost email batch --file ./emails.json
cat ./contacts.json | unitpost contacts imports create --file -
```

`--help` lists these separately under "Convenience flags", so it stays obvious
which flags are the API and which are the CLI's sugar. `--body <json>` (inline
JSON, `@file.json`, or `-`) sets a whole request body when you'd rather not map
fields to flags at all.

### Scripting and CI

- **Set `UNITPOST_API_KEY`** in CI — no `login`, no interactivity, and it beats
  any saved profile.
- **Piped output is always JSON on stdout.** Progress and diagnostics go to
  stderr, so a pipe is never corrupted: `unitpost email send … | jq -r .id` is
  safe. `--json` forces JSON in a terminal, `--quiet` suppresses progress (and
  implies `--json`), `--no-color` / `NO_COLOR` strip escapes.
- **Writes carry an automatic `Idempotency-Key`** (override with
  `--idempotency-key`), so a retried shell command replays instead of
  double-sending.
- **Retries are safe-only**: a read, or a write carrying an idempotency key, on
  `429`/`5xx` with `Retry-After` honored. A keyless write is never retried.
- **Capabilities are enforced server-side.** A key without `emails:send` gets
  the same `403` it would get from cURL — the CLI does not pre-check scopes.

```bash
ID=$(unitpost email send --from you@yourdomain.com --to customer@example.com \
       --subject "Receipt" --html "<p>Thanks</p>" | jq -r .id)
unitpost email get "$ID" | jq -r .status
```

### Exit codes — read them, don't just retry

| Code | Meaning | What to do |
|---|---|---|
| `0` | Success | — |
| `1` | The request was well-formed but failed (API error, auth error) | Read the error; a `403`/`422`/`409` won't fix itself |
| `2` | The invocation itself was wrong (unknown command, missing required flag, a destructive command with no way to confirm) | Fix the command — retrying is pointless |

The split is deliberate: a script can tell "I wrote the command wrong" from
"the API said no" without parsing stderr.

### Credentials and profiles

Resolution order, first hit wins, so an explicit instruction always beats an
ambient one:

1. `--api-key <key>` — one invocation.
2. `UNITPOST_API_KEY` — the CI path.
3. The saved credential for the active (or `--profile <name>`) profile.

`unitpost login` runs OAuth 2.1 + PKCE against the same authorization server
the MCP server uses, storing the token in the OS keychain (a `0600` file where
there's no keychain) and refreshing it automatically. `--no-browser` prints the
URL for an SSH session. Several workspaces coexist as profiles
(`unitpost auth list|switch|rename|remove`), which is normal for anyone with a
staging workspace. `unitpost whoami` reports the source, workspace, scopes and
expiry — never the secret value.

### When you don't know a flag, ask the binary

`unitpost --help` lists the resources, `unitpost <resource>` lists its
commands, and `unitpost <command> --help` prints the flags, enum values and
required-ness straight from the spec. Check there instead of guessing — the
help text is generated, so it can't be stale. `unitpost doctor` diagnoses a
broken setup (version, credential, storage mode, API reachability),
`unitpost completion bash|zsh|fish` prints a completion script, and
`unitpost open` opens the dashboard.

### CLI safety

- **Destructive commands prompt.** Every delete is marked destructive by the
  generator and asks for confirmation; `--yes` skips the prompt, and in a
  non-interactive context (CI, a pipe) a destructive command without `--yes`
  **fails** rather than silently proceeding. Pass `--yes` deliberately, never
  reflexively.
- **A real send is a real send.** `unitpost email send`,
  `unitpost email batch`, and `unitpost email campaigns send` deliver mail to
  real people from a shell with no undo. The confirmation rules in
  [Safety rules](#safety-rules--read-before-acting) apply identically here.
- **Never echo a key into a command you show the user.** Reference
  `$UNITPOST_API_KEY` or tell them to run `unitpost login`. Don't paste a
  resolved key into a snippet, a log, or a commit.

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
- **Follow the Terms and Acceptable Use Policy — no cold outreach.** Unitpost
  is for transactional mail and mail to people who opted in or already have a
  relationship with the sender. Cold outreach (unsolicited sales, prospecting,
  agency/SEO, link-building, partnership or recruiting pitches — B2B included)
  and scraped, purchased, or rented lists are prohibited on every plan
  ([AUP](https://unitpost.com/acceptable-use)). Don't create contacts,
  templates, or campaigns for that purpose, and don't send to a list the user
  can't say they collected with consent. Tell the user the rule and offer the
  compliant path: an opt-in signup form or waitlist, mailing people who asked
  to hear from them, transactional mail. Workspaces that send cold outreach
  are put under review (sending capped) and may be suspended.
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
