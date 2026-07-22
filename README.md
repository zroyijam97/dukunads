# Dukun Ads — Go Ads with AI

**Official documentation for the Dukun Ads AI Connector (MCP).**

Dukun Ads is a Malaysian all-in-one platform for **AI content creation + Meta Ads automation**. It ships with a native **AI Connector (Model Context Protocol / MCP)** so you can drive the whole platform from **Claude** or **ChatGPT** — create campaigns, generate images & videos, manage pixels, pull leads, schedule posts, and read reports, all through natural conversation.

> Live app: **https://dukunads.com**
> Connector URL: **`https://dukunads.com/api/mcp`**

---

## Table of contents

- [What you can do](#what-you-can-do)
- [Connect the AI Connector](#connect-the-ai-connector) (Claude & ChatGPT)
- [How the connector works (OAuth)](#how-the-connector-works)
- [Tool reference (43 tools)](docs/TOOLS.md)
- [Build a landing page with tracking (vibe-code)](docs/LANDING.md)
- [AI Studio credits & pricing](docs/CREDITS.md)
- [Example workflows](#example-workflows)
- [Bahasa Melayu — ringkasan](#bahasa-melayu)

---

## What you can do

Dukun Ads covers six pillars — all reachable from the AI Connector:

| # | Pillar | What the AI can do |
|---|--------|--------------------|
| 1 | **Content creation** | Generate captions, images & videos (SnapGen: Veo, Grok, Seedance, Kling, Nano-Banana, GPT-Image) |
| 2 | **Meta Ads** | Create/read/update campaigns, ad sets & ads; budgets, targeting, reach estimates |
| 3 | **Reporting** | Automatic ad reports to Telegram / WhatsApp / Email; per-campaign + overall summary |
| 4 | **WhatsApp inbox + leads** | Read conversations, manage leads (status, tags, assignment, value) |
| 5 | **Sales pages + Pixel/CAPI + Web analytics** | Track the visitor journey (sessions, sources, device, scroll, time), purchases & revenue; combine behaviour + ads data for conversion advice |
| 6 | **Autopilot** | Create per-account autopilots that post daily on a schedule |

---

## Connect the AI Connector

The connector uses **OAuth 2.1** — there is **no token in the URL**. Anyone who connects logs into **their own** Dukun Ads account, so the URL is safe to share.

**Connector URL**

```
https://dukunads.com/api/mcp
```

### Claude (claude.ai / desktop)

1. Go to **Settings → Connectors**.
2. Click **Add custom connector**.
3. Paste the connector URL: `https://dukunads.com/api/mcp`
4. Click **Connect** → log in to Dukun Ads → **Allow**.
5. The Dukun Ads tools are now available in any chat.

### ChatGPT (Developer mode / custom connector)

1. Go to **Settings → Connectors** (Developer mode).
2. **Add custom connector** / MCP server.
3. Paste the connector URL: `https://dukunads.com/api/mcp`
4. Choose **OAuth** → log in → **Allow**.
5. The tools are now available in chat.

> You can also find your connector URL inside the app at **AI Connector** in the sidebar.

---

## How the connector works

The connector is a standards-compliant MCP server with OAuth 2.1 + PKCE and Dynamic Client Registration:

- **Discovery** — `GET /.well-known/oauth-protected-resource` and `GET /.well-known/oauth-authorization-server`
- **Registration (DCR, RFC 7591)** — `POST /api/mcp/oauth/register`
- **Authorize** — `GET /api/mcp/oauth/authorize` (uses your Dukun Ads login session, PKCE S256)
- **Token** — `POST /api/mcp/oauth/token` (authorization_code + refresh_token)
- **MCP endpoint** — `POST /api/mcp` (Bearer access token)

Unauthenticated requests to `/api/mcp` return `401` with a `WWW-Authenticate` header pointing at the discovery document, which is what triggers the login flow in Claude/ChatGPT. Every request is scoped to the logged-in user — the AI only ever sees that user's own accounts and data.

---

## Example workflows

Just ask in plain language. Behind the scenes the AI calls the right tools.

**Ads reporting**
> "Show my ad account performance for the last 7 days, broken down by campaign."
→ `list_ad_accounts` → `campaign_dashboard`

**Create a campaign (safe by default: PAUSED)**
> "Create a WhatsApp-lead traffic campaign for act_123 with RM30/day, targeting Malaysia."
→ `create_campaign` → `create_ad_set` → `create_ad` (created **PAUSED** for you to review)

**Content**
> "Generate a vertical 8-second video of coffee being poured, cinematic."
→ `list_video_models` → `generate_video` → poll `list_generations`

**Pixel management**
> "List pixels for act_123 and attach pixel 2072… to my new ad set for conversions."
→ `list_pixels` → `attach_pixel`

**Leads**
> "Pull the latest leads from lead form X and mark the hot ones as qualified."
→ `list_lead_forms` → `get_leads` → `update_lead`

**Conversion optimization (behaviour + ads)**
> "My ads spend is up but sales are flat — what should I fix to improve conversions?"
→ `web_analytics` (traffic sources, device, scroll, bounce, conv-rate) + `campaign_dashboard` (spend, CTR, CPC) → AI recommends where to improve

See the full list in **[docs/TOOLS.md](docs/TOOLS.md)**.

---

## AI Studio credits & pricing

Image & video generation is **pay-as-you-go with credits**, priced to match SnapGen. Text generation is free.

- **$10 = 1,000 credits** (top up in multiples of 1,000, paid in MYR via CHIP)
- Prices are shown in **RM** before you generate
- A **receipt** is issued for every top-up and every subscription payment

Full pricing table: **[docs/CREDITS.md](docs/CREDITS.md)**.

---

## Bahasa Melayu

**Dukun Ads — Go Ads with AI.** Platform Malaysia untuk **penjanaan kandungan AI + automasi Meta Ads**. Ia ada **AI Connector (MCP)** supaya anda boleh kawal seluruh platform dari **Claude** atau **ChatGPT** melalui perbualan biasa.

**Sambung connector**

1. Buka **Settings → Connectors** dalam Claude atau ChatGPT.
2. **Add custom connector**.
3. Tampal URL: `https://dukunads.com/api/mcp`
4. **Connect / OAuth** → log masuk Dukun Ads → **Benarkan**.
5. Siap — tools Dukun Ads boleh guna dalam mana-mana chat.

**Kemampuan (6 pilar):** cipta kandungan (imej/video AI), urus Meta Ads (kempen/ad set/ads), laporan automatik (Telegram/WhatsApp/Email), inbox WhatsApp + leads, sales page + Meta Pixel, dan autopilot posting harian.

**Kredit AI Studio:** jana imej & video guna kredit (harga sama seperti SnapGen). **$10 = 1,000 kredit**, topup gandaan 1,000 (bayar RM via CHIP), harga dipapar dalam RM, dan **resit** untuk setiap transaksi. Jana teks percuma.

Rujukan penuh: **[docs/TOOLS.md](docs/TOOLS.md)** · **[docs/CREDITS.md](docs/CREDITS.md)** · **[docs/CONNECTOR.md](docs/CONNECTOR.md)**

---

<sub>Dukun Ads · ASEA Studio · https://dukunads.com</sub>
