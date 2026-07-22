# AI Studio credits & pricing

Dukun Ads AI Studio (image & video generation) is **pay-as-you-go with credits**, priced to match [SnapGen](https://snapgen.ai). **Text generation is free.**

## Conversion

- **$10 = 1,000 credits** → 1 credit = $0.01
- Paid in **MYR** via CHIP (FPX, card, e-wallet)
- Top up in **multiples of 1,000** (1,000 / 2,000 / 3,000 / 5,000 / 10,000)
- The RM price is shown **before** you generate, and again at checkout
- A **receipt** is issued for every top-up (and every subscription payment) — viewable & printable at `/receipt/{id}`

> The USD→MYR rate is configurable by the operator (env `FX_USD_MYR`). At RM4.70/USD, 1,000 credits ≈ RM47.00 and 1 credit ≈ RM0.047.

## Image models (credits per image)

| Model | Credits |
|-------|---------|
| Nano Banana Pro | 3 |
| Nano Banana 2 | 3 |
| Grok Image | 2 |
| Meta AI Image | 3 |
| GPT Image 2 | 10 |

## Video models

Some models charge **per video**, others **per second**. Higher resolution (720p/1080p) costs more.

| Model | Pricing |
|-------|---------|
| Veo 3.1 | 100 / video |
| Veo 3.1 Fast | 4 / video |
| Veo 3.1 Lite | 4 / video |
| Veo 2 | 20 / video |
| Omni Flash | 15 / 20 / 25 / 30 (4s / 6s / 8s / 10s) |
| Grok 3 Video | 480p: 12–24 · 720p: 18–30 (by duration tier 6/10/15s) |
| Seedance 2 | 12/s (480p) · 19/s (720p) |
| Seedance 2 Omni | 19 / second |
| Kling 3.0 | 10/s (720p) · 12/s (1080p) |
| Kling 2.6 | 6/s (720p) · 10/s (1080p) |
| Kling 2.5 | 6/s (720p) · 8/s (1080p) |
| Kling O1 | 10/s (720p) · 12/s (1080p) |

## Behaviour

- Credits are **charged when a generation starts** and **automatically refunded** if generation fails (immediately or asynchronously).
- New accounts start with a small welcome balance so you can try it out.
- When credits run out, generation returns `INSUFFICIENT_CREDITS` with the amount needed and your balance. Top up at **https://dukunads.com/credits**.

## Via the AI Connector

`generate_image` and `generate_video` charge credits just like the web Studio, and return `creditsCharged` + `balance` in their result. If you're out of credits they return `{ error: "INSUFFICIENT_CREDITS", needed, balance }`.
