# Volta — AI-Powered Training Analysis Platform

> Sports performance analysis · Running, cycling and triathlon · Colombia & LATAM · B2C

## Overview

End-to-end sports intelligence platform built for serious athletes who train with discipline but lack the tools to truly understand their progress. Volta connects with athletes' devices (Garmin, COROS), runs a weekly conversational check-in via Telegram, and delivers personalized training analysis with concrete weekly recommendations — plus a full monthly performance report.

**Stack:** n8n · Caddy · Digital Ocean · Gemini 2.5 Flash API · Claude API (V2) · Telegram Bot API · Google Sheets · SendGrid · Cloudflare · GitHub Actions

---

## Problem

Serious athletes train 4–6 times a week, log everything on their devices, and still don't know if they're improving:

* Data is available (Strava, Garmin Connect) but nobody interprets it in context
* Rigid training plans don't account for real life — travel, poor sleep, a hard week at work
* A real coach costs COP 300.000+/month and still doesn't analyze data week by week
* Strava Premium (COP 19.900/month) shows metrics without explaining what they mean
* Runna (COP 34.900/month) gives a fixed plan with no awareness of how the athlete actually felt

---

## Solution

### 1. Device Integration (Garmin & COROS)

OAuth-based read-only access to athlete activity data — distance, time, pace, heart rate, effort, activity type. Each athlete authorizes individually. Data visible only to that athlete. API requests submitted to both Garmin Connect Developer Program and COROS — approval pending.

Webhook endpoint configured at `https://getvolta.pro/webhook/volta-coros` — transparent proxy via Caddy to n8n, so external APIs only see the product domain.

### 2. Conversational Weekly Check-in

Every Sunday, `@volta_pro_bot` sends a personalized check-in on Telegram. Unlike a static questionnaire, the check-in opens with an analysis of the week's data:

> "Hi [name] 👋 This week you ran 32km, your avg pace improved 8 sec vs the last 4 weeks and your zone 2 HR dropped — that's real progress. How did you feel?"

The conversation adapts based on responses. If the athlete mentions discomfort, Volta digs deeper — since when, where, what happened. Injury signals detected before they become a problem.

### 3. Automated Analysis Pipeline

n8n orchestrates the full flow: webhook receives activity data → Gemini 2.5 Flash processes check-in responses + device data → generates personalized analysis + 2-3 concrete weekly recommendations → delivers via Telegram and email.

| Workflow | Trigger | Output |
| --- | --- | --- |
| Volta Onboarding | Form submission at /start | Google Sheet entry + welcome email |
| Weekly Check-in | Every Sunday via bot | Personalized analysis + recommendations |
| Monthly Report | End of month | Full HTML performance report via email |

### 4. Onboarding Flow (3 steps)

Fully functional at `getvolta.pro/start`:

| Step | Content |
| --- | --- |
| 1. Profile | Name, email, primary sport, training goal, available days/week |
| 2. Telegram | Telegram user ID with step-by-step instructions via @userinfobot |
| 3. Device | Garmin Connect + COROS cards (OAuth placeholder — activates on API approval) |

### 5. Web Infrastructure

6 pages in production, SEO/GEO optimized for Colombia and LATAM:

| URL | Purpose |
| --- | --- |
| getvolta.pro | Landing page (ES) |
| getvolta.pro/en | Landing page (EN) — required for Garmin/COROS API applications |
| getvolta.pro/start | Onboarding form |
| getvolta.pro/privacy | Privacy policy (ES) — Law 1581 Colombia compliant |
| getvolta.pro/privacy-en | Privacy policy (EN) — for API partner review |
| getvolta.pro/status | Service status page — required by COROS API |

SEO implementation: meta descriptions, Open Graph, Twitter Card, JSON-LD SoftwareApplication schema, canonical URLs, og:locale es_CO / en_US, robots index/follow.

### 6. Brand Identity

Custom logo system designed from scratch: V symbol (two thick strokes + horizontal line + circle) in electric green (#00FF87), with dark (#0A0A0A) and light (#F8F8F6) variants. Favicon (transparent background), apple-touch-icon, and OG image (1200×630) generated and deployed.

App icons submitted to COROS at required sizes: 300×300, 144×144, 120×120, 102×102 px.

### 7. Email System

Transactional HTML email sent from `start@getvolta.pro` via SendGrid on registration. Email includes athlete profile recap, 3 onboarding next steps, Telegram bot link, and a personal founder message. SPF and DKIM configured in Cloudflare.

---

## Key Design Decisions

**Telegram over a native app** — No app development cost, no App Store friction, no downloads. Telegram has near-universal penetration in the target demographic. The weekly check-in takes under 2 minutes and lives where athletes already are.

**Gemini 2.5 Flash over GPT or Claude (for now)** — Gemini Flash allows the pilot to run at minimal variable cost during early validation. Migration to Claude API planned for V2 when conversational quality becomes the primary differentiator at scale.

**n8n over custom backend** — Full automation without a dedicated engineering team. Each workflow is independently maintainable, observable, and extensible. The entire product runs on a $12/month Digital Ocean droplet shared with a parallel project.

**Caddy over nginx** — The existing stack used Caddy via Docker. Rather than introduce nginx complexity, Caddy's `handle` directive was used to add webhook proxy routing alongside static file serving — keeping the infrastructure simple.

**Separate privacy policy in English** — Required by both Garmin Connect and COROS developer programs for API approval. A Spanish-only policy was an immediate rejection risk.

**getvolta.pro over .com or .co** — Available, affordable ($2.48/year), and the `.pro` TLD reinforces the "serious athlete" positioning without geographic limitation.

**Co-marketing applied with COROS API** — In addition to API access, a co-marketing request was included in the COROS application with a June 1, 2026 launch date. This unlocks placement on COROS's partner page and coordinated marketing announcements at no cost.

---

## Status

| Area | Status |
| --- | --- |
| Web (6 pages) | ✅ Live — getvolta.pro |
| Onboarding flow | ✅ Live — getvolta.pro/start |
| n8n workflows | ✅ Active — onboarding + email |
| Bot @volta_pro_bot | ✅ Active on Telegram |
| Brand identity & logo | ✅ Complete |
| SEO / GEO optimization | ✅ Implemented |
| Email SPF + DKIM | ✅ Configured |
| Garmin Connect API | ⏳ Application submitted Mar 21, 2026 |
| COROS API + co-marketing | ⏳ Application submitted Mar 25, 2026 |
| Strava Extended Access | ⏳ Request submitted |
| Checkout / Stripe | 🔄 Planned for June 2026 |
| Claude API migration | 🔄 Planned for V2 |
| GitHub Actions auto-deploy | 🔄 In progress |

---

## Live Product

[getvolta.pro](https://getvolta.pro) · [getvolta.pro/en](https://getvolta.pro/en) · [@volta_pro_bot](https://t.me/volta_pro_bot)
