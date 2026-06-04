# Realtor OS

A productized **Claude Cowork** plugin suite for real estate agents, by **STOYC**. Install from this
GitHub marketplace; everything is markdown + JSON — no build step.

## Packages

| Package | What it does | Status |
|---------|--------------|--------|
| **Core** | Daily command center — CRM-aware briefings, lead replies & follow-ups in the agent's voice, appointment prep, contact research, call recaps, open-house capture, and live dashboards. Plus guided onboarding, voice personalization, and self-improving memory. | ✅ This repo |
| **Marketing, Content & SEO** | Listing marketing rollouts, social posts, newsletters, blogs, ads, content calendar, Google Business Profile + reviews, and a website SEO/GEO audit — all in the agent's voice. | ✅ This repo |
| **Listings & Market Intel** | CMAs, market reports, property & neighborhood research, and buyer-match — researched live from the web with cited, verify-before-client guardrails. No MLS required. | ✅ This repo |
| **Prospecting & Sphere** | Sphere Radar — watches your sphere for the life & money moments before a move (sale, relocation, promotion, new baby, award), verifies from news/LinkedIn, and drafts warm no-ask notes in your voice. On demand or weekly on autopilot. | ✅ This repo |

## Install (Claude Cowork)

1. In Cowork open the plugin **Directory → Plugins → +  → Add marketplace**.
2. Enter this repo: `stoyc-ai/realtor-os` (or its git URL `https://github.com/stoyc-ai/realtor-os`) → **Sync**.
3. Install **Realtor OS — Core** from the list.

## Connect your tools

See [`core/CONNECTORS.md`](core/CONNECTORS.md). In short:
- **CRM (Follow Up Boss):** add the custom connector URL STOYC gives you (Settings → Connectors → Add custom connector). Your API key stays on STOYC's server — see the companion `fub-mcp-server`.
- **Native:** connect Gmail, Google Calendar, Google Drive.
- **Working folder:** pick one so your voice profile and memory persist.

## First-run onboarding (5 minutes)

Just run **`/onboard`** (or say *"onboard me"*). Your assistant walks you through setup with
clickable buttons — service areas & ZIP codes, price band, niche, voice & tone, and your rules —
plus a spot to paste sample listings, emails, and captions so everything sounds like you. Pick a
working folder when asked so it all persists.

Then say **"start my day"** for your briefing, or **"open my dashboard"** for the command center.

## What's inside Core

**Foundation (used everywhere):** `learn-my-voice`, `remember`
**Daily workflow:** `daily-briefing`, `lead-reply`, `follow-up`, `appointment-prep`, `contact-research`, `call-recap`, `open-house-capture`
**Dashboards (live artifacts):** `dashboards` → Daily Command Center · Hot Leads · Pipeline · Commission Forecast

Skills auto-fire from natural language. Anything that writes to your CRM always shows you the change
and waits for your confirmation first.
