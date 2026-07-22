# Tool reference

The Dukun Ads AI Connector exposes **43 tools**. Every tool is scoped to the logged-in user. Below, `accountId` means a Meta ad-account id like `act_1234567890` (get it from `list_ad_accounts`).

> Tip for the AI: for any ads task, **always start with `list_ad_accounts`** to get the `accountId`.

## Meta Ads

| Tool | Purpose | Key params |
|------|---------|-----------|
| `list_ad_accounts` | **First step.** List your Meta ad accounts (`act_…` + name). | — |
| `ad_account_funding` | Funding source, balance, spend cap for an account. | `accountId` |
| `list_campaigns` | Campaigns for an account + quick insights. | `accountId`, `datePreset` (`last_7d`, `last_30d`, `this_month`) |
| `create_campaign` | Create a campaign (**PAUSED** by default). | `accountId`, `name`, `objective`, … |
| `update_campaign` | Pause/resume or rename. | `campaignId`, `status` (`ACTIVE`/`PAUSED`) |
| `campaign_dashboard` | **One call:** account totals + link clicks + per-campaign breakdown. | `accountId?`, `datePreset` (default `today`) |
| `ad_insights` | Insights for account/campaign/ad set/ad + breakdowns. | `accountId`, `datePreset`, `breakdown?` |
| `ads_usage` | Active slots, active ad accounts, est. monthly cost. | — |

### Ad sets & ads

| Tool | Purpose | Key params |
|------|---------|-----------|
| `create_ad_set` | Create an ad set (never send `status`; parent campaign is PAUSED). For conversion ads, bind a pixel directly here. | `accountId`, `name`, `campaignId`, `optimizationGoal`, `destinationType`, `countries`, `dailyBudgetSen`, `promotedObjectPageId?`, `promotedObjectPixelId?`, `promotedObjectCustomEventType?` (PURCHASE/LEAD/…) |
| `create_ad` | Create an ad. | `accountId`, `adName`, `adSetId`, `pageId`, `format`, `primaryText`, `headline`, `callToAction`, `destinationUrl`, `imageUrl` |
| `update_ad_set` | ON/OFF or edit an ad set. | `adSetId`, `status` (`ACTIVE`/`PAUSED`), `name?`, `dailyBudgetSen?` |
| `update_ad` | ON/OFF or edit an ad. | `adId`, `status`, `name?` |
| `list_facebook_pages` | Real Meta **Page IDs** (needed for `promotedObjectPageId` / `create_ad`). | — |

### Targeting & audiences

| Tool | Purpose | Key params |
|------|---------|-----------|
| `list_audiences` | Custom/lookalike audiences for an account. | `accountId` |
| `reach_estimate` | Estimated audience size for a targeting spec. | `accountId`, targeting spec |
| `targeting_search` | Search Meta targeting taxonomy. | `type` (`adinterest`/`adgeolocation`/`adlocale`), `q` |

## Lead Ads & CRM

| Tool | Purpose | Key params |
|------|---------|-----------|
| `list_lead_forms` | Lead-ad forms on a Facebook Page. | `pageId` |
| `get_leads` | Pull all leads from a lead form. | `formId` |
| `list_leads` | CRM leads. | `status?` (`new`/`engaged`/`qualified`/`closed`/`lost`) |
| `update_lead` | Update a lead. | `leadId`, `status?`, `assignee?` (`ai`/`human`), `tags?`, `value?` |
| `list_conversations` | Recent inbox threads (WhatsApp/Messenger/IG). | `status?` (`open`/`needs_human`/`closed`) |

## Meta Pixel (campaign / ad-set / ad level)

| Tool | Purpose | Key params |
|------|---------|-----------|
| `list_pixels` | Pixels for an account (id, name, code). | `accountId` |
| `create_pixel` | **Create a new Meta Pixel** (via AdFlow — no Events Manager needed). | `accountId`, `name` |
| `attach_pixel` | Attach a pixel to an ad set (campaign & ads inherit). Sets conversion optimization. | `accountId`, `adSetId`, `pixelId`, `conversionEvent?` |
| `pixel_stats` | Verify a pixel & read events (empty = no events yet). | `accountId`, `pixelId` |

## Content creation (AI Studio) — **charges credits**

| Tool | Purpose | Key params |
|------|---------|-----------|
| `generate_text` | Generate caption/text (OpenRouter). **Free.** | `prompt`, `model?` |
| `generate_image` | Generate an image (SnapGen). **Charges credits.** | `prompt`, `model?`, `aspectRatio?` |
| `list_video_models` | Video models + supported durations. Call before `generate_video`. | — |
| `generate_video` | Generate a video (SnapGen). **Charges credits.** | `prompt`, `model?`, `duration?`, `resolution?`, `aspectRatio?` |
| `list_generations` | Latest 15 generations + status + result URL. | `kind?` |

> Image/video tools return `creditsCharged` and `balance`. If credits are insufficient they return `{ error: "INSUFFICIENT_CREDITS", needed, balance }` — top up at `https://dukunads.com/credits`. See [CREDITS.md](CREDITS.md).

## Reporting

| Tool | Purpose | Key params |
|------|---------|-----------|
| `list_ads_reports` | All automatic ad-report configs. | — |
| `get_ads_report` | Report config for one account (secrets masked). | `accountId` |
| `set_ads_report` | Create/update a report (channel, frequency, fields). | `accountId`, `channel`, `frequency`, `reportFields?`, credentials |
| `send_ads_report` | Send the report **now**. | `accountId` |

Frequencies: `5m`, `15m`, `30m`, `hourly`, `daily`, `weekly`. Channels: Telegram, WhatsApp (Cloud API or QR gateway), Email. Reports include an **overall summary** + optional **per-campaign** breakdown.

## Autopilot & sales

| Tool | Purpose | Key params |
|------|---------|-----------|
| `list_autopilots` | All autopilots (per social account). | — |
| `create_autopilot` | Create an autopilot (many per account allowed). | `accountId`, `mode` (`text`/`image`/`video`), `times` (`HH:mm`, KL time) |
| `list_sales_pages` | Sales pages + conversions & revenue + trackKey + install snippet URL. | — |
| `create_sales_page` | **Create a sales page + tracking code** (Meta Pixel + conversion + behaviour). Returns trackKey + `<script>` snippet. Bind a pixel with `pixelId` + `accountId` → server-side CAPI via AdFlow, no token. | `name`, `url?`, `pixelId?`, `accountId?`, `currency?` |
| `list_conversions` | Raw conversion events for a page (or all): event, RM value, source/medium/campaign, fbclid, email, eventId, time. | `salesPageId?`, `days?`, `limit?` |
| `web_analytics` | Visitor journey/behaviour analytics: sessions, conversion rate, revenue, bounce, avg scroll & time, breakdown by traffic source / device / campaign. **Combine with `ad_insights` / `campaign_dashboard` to recommend conversion improvements.** | `salesPageId?`, `days?` (default 30) |

---

## Notes for AI agents

- **Ads are created PAUSED** by default — tell the user to review before activating.
- **`create_ad_set` / `create_ad` must not send `status`** (only campaigns accept it).
- Use **real Meta Page IDs** (`list_facebook_pages`) for `promotedObjectPageId` and `create_ad.pageId`, not internal account ids.
- For bid: default `bidStrategy: "LOWEST_COST_WITHOUT_CAP"` needs no bid amount.
- Video generation is asynchronous — poll `list_generations` for the result URL.
