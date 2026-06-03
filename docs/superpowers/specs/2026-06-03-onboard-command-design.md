# Design — `/onboard` button-driven setup

**Date:** 2026-06-03
**Package:** realtor-os / core
**Status:** Approved, ready for implementation plan

## Problem

New agents who install the plugin and connect GitHub need a fast, dead-simple way to
tell their assistant everything it needs to be effective: business basics, service
areas, ZIP codes, price band, niche, tone/voice, operating rules, and writing samples.

The current `onboarding-agent` skill does this conversationally, asking everything as
**plain-text questions** — a wall of typing for a non-technical realtor. We want the
questions to appear as **clickable buttons** so setup is mostly tapping, and the whole
experience should feel like **hiring and briefing a new 24/7 executive assistant**.

## Goals

- A clean `/onboard` slash command as the first-day entry point ("everyone runs this").
- Questions presented as **clickable buttons** wherever the answer is a choice.
- Comprehensive capture (12+ fields) but finishable in a few minutes — every screen is a
  quick tap with smart defaults and a Skip path (honors the project's ease-of-use rule).
- Explicit, generous capture of **writing samples** (listing descriptions, example
  emails, social captions, bio, newsletters).
- A free-text **catch-all** for "anything else your assistant should know."
- **EA framing** throughout — warm, personal, "I'm your new assistant learning how you work."
- Saves to the same local files every other skill already reads.

## Non-goals

- No server-side hosting in this iteration (local files only; STOYC-managed notes retained).
- No new MCP tools or connectors.
- Slash command is `/onboard` (slash commands cannot contain a space, so not `/onboard me`);
  the existing "onboard me" text trigger still works and routes to the same flow.

## Approach

**One flow, two entry points, single source of truth.**

- New thin `core/commands/onboard.md` slash command whose body simply invokes the
  rewritten `onboarding-agent` skill in button mode.
- The `onboarding-agent` skill is **rewritten** to drive the flow with the
  `AskUserQuestion` tool (which renders 2–4 tappable options per question, up to 4
  questions per screen, always with an automatic "Other" free-text box).
- Typing "onboard me" / "get started" still triggers the same skill — both paths converge,
  nothing can drift.

### Why buttons = `AskUserQuestion`

The clickable-button experience is produced by the `AskUserQuestion` tool. Each call can
present up to 4 questions, each with 2–4 options, single- or multi-select, and an
automatic **Other** option for typing a custom answer. Fields that are inherently
free-text (exact ZIP list, sign-off, writing samples, catch-all) are captured via the
Other box or a short paste prompt — buttons are not forced onto them.

## The flow

Framed end-to-end as briefing a new executive assistant. Greeting sets the tone:

> "Think of me as your new 24/7 assistant. Answer a few quick taps and I'll learn how you
> work, who you serve, and how you sound — so everything I do from here feels like *you*."

Show the running checklist after each step; end on the "you're all set" summary.

### Step 0 — Working folder
Confirm a working folder is selected (nothing saves without it). Unchanged from current skill.

### Step 1 — Business basics (1 AskUserQuestion call, 4 questions)
1. **Brokerage** — Other = type it.
2. **Primary price band** — `<$300k` · `$300–600k` · `$600k–1M` · `$1M+`.
3. **Service-area scope** — `1 town` · `2–3 towns` · `Whole county` · `Multi-county`.
   Follow with a short paste prompt for exact towns + ZIP codes (free text).
4. **Languages** — multi-select: `English` · `Spanish` · `Other`.

### Step 2 — Who they serve (1 call, 3 questions, multi-select)
5. **Niche** — `First-time buyers` · `Luxury` · `Investors` · `Sellers/listings` · `Relocation`.
6. **Lead sources** — `Referrals/SOI` · `Zillow/portals` · `Social ads` · `Open houses` · `Past clients`.
7. **Content channels** — `Instagram` · `Facebook` · `Email` · `Blog` · `YouTube/Reels`.

### Step 3 — Voice & tone (1 call, 3 questions)
8. **Tone** — multi-select: `Warm` · `Professional` · `Bold/punchy` · `Data-driven` · `Playful`.
9. **Emoji usage** — `None` · `Light` · `Heavy`.
10. **Sign-off** — Other = type it.

### Step 4 — Operating rules (1 call, 3 questions)
11. **Follow-up speed** — `Within 1 hr` · `Same day` · `Next day`.
12. **Days off** — multi-select day buttons.
13. **Hard rules** — multi-select: `Never cold-text` · `Always include address` · `No price talk in DMs` · Other.

### Step 5 — Writing samples (rich paste capture)
Explicitly invite, each as its own paste area (any subset is fine; Drive/Gmail pull offered
if connected):
- Listing descriptions
- Example emails (a lead reply, a follow-up)
- Social media captions
- Bio / "about me"
- Newsletters

Hands the collected samples to **learn-my-voice** to build `voice-profile.md`.

### Step 6 — Catch-all
> "Last thing — anything else your assistant should know? Quirks, do's and don'ts,
> favorite phrases, things clients always ask. Type as much or as little as you like."

Free text, saved to `CLAUDE.md` under a "Notes for my assistant" section.

### Wrap-up
Existing checklist + "You're all set, [Name]!" summary + suggested first task ("start my day").

## Data / persistence

- **Business facts, rules, areas, ZIPs, price band, niche, channels, sign-off, catch-all**
  → `CLAUDE.md` via the **remember** skill.
- **Tone, emoji, writing samples** → `voice-profile.md` via the **learn-my-voice** skill.
- No new files or formats; consumers (all content skills, daily-briefing, etc.) already read these.

## Files changed

- **New:** `core/commands/onboard.md` — thin trigger; frontmatter `description`, body invokes the skill in button mode.
- **Rewrite:** `core/skills/onboarding-agent/SKILL.md` — replace plain-text Q&A with the
  `AskUserQuestion` batches above; add rich sample capture (Step 5), catch-all (Step 6), and
  EA framing; keep the 5-step checklist, output summary, and STOYC-managed notes.
- **Bump:** `core/.claude-plugin/plugin.json` version.

## Edge cases / guardrails

- **No working folder selected** → ask for one before any save (existing behavior).
- **Skip / no answer** → every screen is skippable; record only what's provided, never block.
- **Other free-text** → captured verbatim; for areas/ZIPs use a paste prompt, not buttons.
- **Already STOYC-managed** → offer to check `get_my_profile` and skip what's done (retained).
- **Fair-housing guardrail** in `voice-profile.md` retained from learn-my-voice.

## Success criteria

- Running `/onboard` (or "onboard me") presents clickable button screens, not a text wall.
- After completion, `CLAUDE.md` and `voice-profile.md` exist in the working folder with the
  captured fields, including writing samples and the catch-all note.
- The flow reads like briefing a new EA and finishes in a few minutes.
