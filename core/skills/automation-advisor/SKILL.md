---
name: automation-advisor
description: >
  Personal automation consultant that finds what the agent should put on autopilot. Use when the agent
  says "help me automate", "what can I automate", "what should I automate", "find automations for me",
  "put my work on autopilot", "set up automations", or "automate my business". Asks a few clickable
  questions about their bottlenecks, the tools they use, and the connectors they've set up, then
  recommends specific Realtor OS automations — each with a ready-to-schedule instruction — tailored to
  what they actually have connected.
metadata:
  version: "1.0.0"
---

# Automation Advisor

Most agents waste hours on repetitive work a system could do for them — they just don't know which
parts to hand off or how. This skill plays consultant: it asks a few quick tap-through questions
about where their time goes, what tools they use, and what they've connected, then hands back a
**prioritized, personalized list of automations** — each mapped to a real Realtor OS skill and a
ready-to-paste scheduled-task instruction. The goal: walk away with 2–4 automations set up the same
day, not a vague "you could automate stuff."

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## Button mode

**Ask the choice questions as clickable buttons using the `AskUserQuestion` tool** (up to 4 questions
per screen, 2–4 options each, `multiSelect: true` where more than one applies). Every question gets an
automatic "Other" box.

> **HARD RULE — never call `AskUserQuestion` for a free-text-only question.** It requires 2–4 preset
> options and errors without them. Anything open-ended (e.g. "describe your busiest day") is asked as
> a plain chat message, never a button card.

## How it works

```
ALWAYS (works standalone)
  ✓ Three quick tap-through screens → a tailored automation plan with ready-to-schedule prompts
  ✓ Output: prioritized automations, each with what it does, what powers it, and what it needs
SUPERCHARGED (when connected)
  + ~~crm: read get_my_profile to skip the "what do you do" basics and tailor harder
  + Detect live connectors so recommendations match exactly what they can run today
```

## Before you start

- **Call `get_my_profile`** (on `~~crm`) for the agent's niche, areas, and rules; fall back to
  `CLAUDE.md`. This lets you tailor recommendations (e.g. a luxury listing agent vs. a buyer's agent).
- **Note which connectors look live.** If you can tell which tools are connected (CRM, Gmail, Calendar,
  Drive, etc.), use it — but still confirm with the connector question below, since that decides which
  automations are recommendable *today* vs. flagged as "connect X first."

## The questions (3 AskUserQuestion screens)

### Screen 1 — Where does your time go? *(multi-select)*
"What eats the most of your time, or slips through the cracks?"
- `Following up with leads` · `Responding to new leads fast` · `Staying in touch with past clients/sphere`
- `Posting content / social` · `Market updates for clients` · `CRM notes & data entry`
- `Scheduling / calendar` · `Reviews & reputation` · `Prepping for appointments`

### Screen 2 — What tools do you use? *(multi-select)*
"Which of these are part of your day-to-day?" (This tells me what to plug into.)
- `Follow Up Boss (CRM)` · `Gmail / Outlook` · `Google Calendar` · `Google Drive / Sheets`
- `Instagram / Facebook` · `Canva` · `Mailchimp / email marketing` · `A website` · `YouTube / Reels`

### Screen 3 — What have you connected in Claude? *(multi-select)*
"Which of these have you already connected (Settings → Connectors)? This decides what we can turn on
right now." (Free-text "Other" allowed.)
- `My CRM` · `Gmail` · `Google Calendar` · `Google Drive` · `Canva` · `Mailchimp` · `Google Business Profile` · `None yet`

If something open-ended comes up ("I also do property management"), ask it as a plain chat follow-up —
not a button card.

## How to turn answers into automations

Map each stated bottleneck to a real Realtor OS skill/agent, then **gate it by connectors**: recommend
what runs today, and clearly flag anything that needs a connector first (turn that into a next step).

| If they struggle with… | Recommend | Powered by | Needs |
|------------------------|-----------|------------|-------|
| Morning chaos / no plan | Daily auto-briefing every weekday AM | `morning-auto-briefing` / `daily-command` | ~~crm, Calendar |
| Slow lead response | Speed-to-lead auto-responder | `new-lead-responder` | ~~crm, Gmail |
| Follow-ups slipping | Friday "loose ends" digest of who you owe a reply | `follow-up` (+ Gmail review) | Gmail (CRM optional) |
| Calls not getting made | Daily "who to call" list | `who-to-call` | ~~crm |
| Cold/old database | Weekly database reactivation batch | `database-reactivation` | ~~crm |
| Few referrals / sphere | Weekly Sphere Radar (life & money moments) | `sphere-radar` (Prospecting pkg) | Drive sheet or ~~crm |
| Inconsistent content | Monthly content calendar + scheduled market-update posts | `content-calendar`, `market-update-post` | none (Canva/IG optional) |
| Reviews ignored | Draft replies to new Google reviews | `review-response` | ~~gbp (else draft-to-paste) |
| Listing launches | One-command listing rollout | `listing-launcher` (Marketing pkg) | none (Canva/email optional) |
| Appointment prep | Auto-prep brief before each appointment | `appointment-prep` | ~~crm, Calendar |

Rules:
- **Only recommend as "ready now" what their connected tools support.** If a great automation needs a
  connector they don't have, list it under "Worth connecting next" with the one-line reason.
- **Prioritize by impact × effort** — lead/referral/income automations first, nice-to-haves last.
- **Cap it.** Suggest the top 3–4 to start, not all ten — overwhelm kills follow-through.
- Most automations run as **Cowork Scheduled tasks**; give a copy-paste instruction for each.

## Output format

```markdown
# Your automation plan — start with these [N]

🔝 **Start here:** [the single highest-impact one], because [reason tied to what they told you].

| Automation | What it does for you | Runs | Powered by | Ready? |
|-----------|----------------------|------|------------|--------|
| [name] | [plain benefit] | [e.g. "Mon 8am, weekly"] | [skill] | ✅ now / 🔌 connect [X] |

### Set them up (copy into Cowork → Scheduled → New task)
**1. [Automation name]**
> "[ready-to-paste scheduled-task instruction, with their areas/niche filled in]"

**2. [Automation name]**
> "[instruction]"

### Worth connecting next
- **[Connector]** → unlocks [automation], which would [benefit]. Connect in Settings → Connectors.

*Want me to set the first one up with you right now, or tweak any of these?*
```

## Execution flow

1. **Load context** (`get_my_profile` → `CLAUDE.md`); note any connectors you can already see.
2. **Screen 1 — bottlenecks** (AskUserQuestion, multi-select).
3. **Screen 2 — tools they use** (AskUserQuestion, multi-select).
4. **Screen 3 — connectors set up** (AskUserQuestion, multi-select) — this gates "ready now."
5. **Map & prioritize** bottlenecks → skills/agents using the table; gate by connectors; pick the top 3–4.
6. **Output the plan** with copy-paste scheduled-task instructions, personalized with their niche/areas.
7. **Offer to set the first one up** and to schedule it. Keep it to a few high-impact wins.

## Related skills

- **onboarding-agent** — if they haven't set up voice/business yet, run that first so automations sound like them.
- **morning-auto-briefing**, **sphere-radar**, **database-reactivation**, **content-calendar** — common automation targets this skill recommends.
- **remember** — save any durable preference they mention (e.g. "I never want Sunday tasks").
