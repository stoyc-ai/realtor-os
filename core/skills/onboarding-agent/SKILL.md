---
name: onboarding-agent
description: >
  Friendly setup wizard that gets a brand-new agent fully up and running by talking to them like
  they just hired a new 24/7 assistant. Use when the agent runs /onboard or says "set me up",
  "onboard me", "get started", "set up my workspace", "help me get started", or it's clearly their
  first time. Presents setup questions as clickable buttons (via AskUserQuestion) — areas, ZIP codes,
  price band, niche, voice/tone, rules — plus rich writing-sample capture and a free-text catch-all,
  then saves everything locally so all future work sounds like them and knows their business.
metadata:
  version: "2.0.0"
---

# Onboarding Agent

Get a new agent set up in a few minutes — mostly by **tapping buttons**, not typing. Frame the whole
experience as **briefing a new 24/7 executive assistant**: warm, personal, "I'm here to learn how you
work so everything I do feels like you." Keep it quick and encouraging the whole way.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## Button mode (the core of this skill)

**Present every question that has set answers as clickable buttons using the `AskUserQuestion` tool**,
not as plain text the agent has to read and type back. This is the whole point of the experience.

- Each `AskUserQuestion` call can show **up to 4 questions at once**, each with **2–4 real options**.
- Use `multiSelect: true` when more than one answer can apply (niche, lead sources, channels, tone, days off, rules).
- Every question automatically gets an **"Other"** box, so the agent can type a custom answer anytime.
- **Everything is skippable.** If they skip a screen, record nothing for it and move on — never block.

> **HARD RULE — never call `AskUserQuestion` for a free-text-only question.** The tool requires
> 2–4 predefined options; if you call it for an open "type it out" answer (no real options), it
> **fails** with an error card. So the following are asked as a **normal chat message**, never as a
> button card: **exact towns/neighborhoods + ZIP codes, sign-off, the writing samples, and the
> "anything else" catch-all.** Only use `AskUserQuestion` for the choice questions that have set
> options (brokerage, price band, area scope, languages, niche, lead sources, channels, tone, emoji,
> follow-up speed, days off, hard rules). When in doubt — if you can't list 2–4 concrete tappable
> options — just ask in plain text.

## Self-setup vs. STOYC-managed

There are two ways an agent gets set up:

- **Self-setup (this skill):** the agent runs this wizard and everything saves locally to their
  working folder. They're ready to go in minutes.
- **STOYC-managed:** for managed/agency clients, STOYC sets the profile up **server-side** instead —
  the agent does nothing and skills read voice + business info straight from the `~~crm` connector's
  `get_my_profile` tool. This wizard can also be used to **generate** a profile that STOYC then loads
  server-side. Either way, the steps below are the same.

So: use this skill for self-setup, or to build a profile for STOYC to host. If the agent is already
managed by STOYC, let them know they may already be set up and offer to check with `get_my_profile`.

## How it works

```
ALWAYS (works standalone)
  ✓ Tap-through setup — buttons for the choices, quick paste boxes for samples & notes
  ✓ Output: a progress checklist as you go + a "you're all set" summary + a first thing to try
SUPERCHARGED (when connected)
  + Google Drive / Gmail: pull existing posts, listings, and sent emails as voice samples
  + ~~crm: confirm a server-side profile already exists, so steps can be skipped
```

## The flow

Run these in order, one screen at a time. After each `AskUserQuestion` screen, show the running
checklist so they always see how close they are to done.

### Greeting (set the EA frame)

Open with something like:

> "Think of me as your new 24/7 assistant. Answer a few quick taps and I'll learn how you work, who
> you serve, and how you sound — so everything I do from here feels like *you*. Takes about 5 minutes."

If managed by STOYC, offer to check `get_my_profile` first and skip what's already done.

### Step 0 — Pick a working folder

This is where their settings save so they don't have to repeat themselves next time. Nothing else can
be saved until this is done, so don't skip it. If they don't have one, suggest creating a simple
"Realtor OS" folder.

### Step 1 — Business basics  *(one AskUserQuestion call — 4 questions)*

1. **Brokerage** — options of a few common ones + "Other" to type theirs.
2. **Primary price band** — `Under $300k` · `$300–600k` · `$600k–$1M` · `$1M+`.
3. **Service-area scope** — `1 town` · `2–3 towns` · `Whole county` · `Multi-county`.
4. **Languages** — multi-select: `English` · `Spanish` · `Other`.

Then, as a **normal chat message** (NOT an `AskUserQuestion` card — there are no options to tap):
"Which exact towns/neighborhoods do you cover, and the ZIP codes? Just list them here — or skip."

### Step 2 — Who they serve  *(one AskUserQuestion call — 3 multi-select questions)*

5. **Niche** — `First-time buyers` · `Luxury` · `Investors` · `Sellers/listings` · `Relocation`.
6. **Lead sources** — `Referrals/SOI` · `Zillow/portals` · `Social ads` · `Open houses` · `Past clients`.
7. **Content channels** — `Instagram` · `Facebook` · `Email` · `Blog` · `YouTube/Reels`.

### Step 3 — Voice & tone  *(one AskUserQuestion call — 3 questions)*

8. **Tone** — multi-select: `Warm` · `Professional` · `Bold/punchy` · `Data-driven` · `Playful`.
9. **Emoji usage** — `None` · `Light` · `Heavy`.
10. **Sign-off** — a few common ones + "Other" to type theirs.

### Step 4 — Operating rules  *(one AskUserQuestion call — 3 questions)*

11. **Follow-up speed** — `Within 1 hr` · `Same day` · `Next day`.
12. **Days off** — multi-select day buttons (Mon–Sun + `None`).
13. **Hard rules** — multi-select: `Never cold-text` · `Always include the address` · `No price talk in DMs` · "Other".

### Step 5 — Writing samples (so everything sounds like them)

This is what makes content sound like *them* instead of generic AI. Invite samples generously — any
subset is fine. Offer each as its own quick paste area:

> "Paste me some of your real writing so I can sound like you. Drop in whatever you've got — no need
> for all of it:
> - **Listing descriptions** (1–2)
> - **Example emails** — a lead reply and a follow-up
> - **Social media captions** (3–5 of your favorites)
> - **Bio / 'about me'**
> - **A newsletter or two**"

If Google Drive or Gmail is connected, offer to pull these automatically instead of pasting. Hand the
collected samples to the **learn-my-voice** flow to build `voice-profile.md`.

### Step 6 — Anything else  *(free text catch-all)*

> "Last thing — anything else your assistant should know? Quirks, do's and don'ts, favorite phrases,
> questions clients always ask, how you like to work. Type as much or as little as you want."

Save this verbatim to `CLAUDE.md` under a **"Notes for my assistant"** section, via the **remember** flow.

### Wrap-up — confirm and try the first thing

> "That's it — you're set up! Try saying **'start my day'** and I'll pull together your briefing."

## Where each answer is saved

Use the **remember** flow to save business facts to `CLAUDE.md`, and the **learn-my-voice** flow to
build `voice-profile.md`. Both live in the agent's **selected working folder** (in Cowork, always write
to the working folder, not a relative path).

- **`CLAUDE.md`** (via remember): brokerage, price band, service areas + ZIP codes, languages, niche,
  lead sources, content channels, sign-off, follow-up speed, days off, hard rules, and the "Notes for
  my assistant" catch-all.
- **`voice-profile.md`** (via learn-my-voice): tone, emoji usage, and all the writing samples.

No new files or formats — every other skill (`lead-reply`, `follow-up`, listing/social/newsletter
content, `daily-briefing`) already reads from these two.

## Output format

Show the checklist as you go (check off each step as it completes), then the summary at the end.

```markdown
## Setup progress
- [x] 1. Working folder selected → [folder name]
- [x] 2. Business basics saved → CLAUDE.md
- [x] 3. Who you serve saved → CLAUDE.md
- [x] 4. Voice & tone saved → voice-profile.md
- [x] 5. Writing samples captured → voice-profile.md
- [x] 6. Notes for your assistant saved → CLAUDE.md
- [ ] 7. Tools connected → CRM + Gmail/Calendar (in Settings → Connectors)
- [ ] 8. First task tried

---

## You're all set, [Name]! 🎉

Here's what your new assistant now knows:
- **Brokerage:** [brokerage]
- **Service areas:** [areas + ZIPs]
- **Price band:** [band]
- **Niche:** [niche]
- **Voice:** captured from [N] samples — everything I write will sound like you
- **Rules I'll follow:** [1–2 key rules]

**Still optional:** connect your CRM, Gmail/Calendar, Canva, and Mailchimp in Settings → Connectors to unlock more.

**Try this first:** say **"start my day"** for your morning briefing — or "launch this listing" the next time you go live.
```

## Execution flow

1. **Greet with the EA frame** — "I'm your new 24/7 assistant; a few quick taps and I'll learn how you work." If managed by STOYC, offer to check `get_my_profile` and skip what's done.
2. **Step 0 — working folder.** Confirm one is selected; nothing saves without it.
3. **Steps 1–4 — buttons.** Run each as an `AskUserQuestion` screen (business basics, who they serve, voice & tone, rules). Add the towns/ZIPs paste prompt after Step 1. Save to `CLAUDE.md` / `voice-profile.md` as you go.
4. **Step 5 — samples.** Invite listings, emails, captions, bio, newsletters (or pull from Drive/Gmail); hand to **learn-my-voice**.
5. **Step 6 — catch-all.** Save the free-text note to `CLAUDE.md` via **remember**.
6. **Wrap-up.** Show the "you're all set" summary and suggest "start my day."
7. **Keep the checklist visible** after each step. Stay warm, quick, and encouraging — like a great new hire eager to learn the ropes.

## Related skills

- **learn-my-voice** — builds the `voice-profile.md` from the samples in Step 5.
- **remember** — saves the business basics, rules, and catch-all note into `CLAUDE.md`.
- **daily-briefing** — the suggested first task ("start my day") once setup is done.
