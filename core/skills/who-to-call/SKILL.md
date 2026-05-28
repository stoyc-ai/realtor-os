---
name: who-to-call
description: >
  A ranked "call these people today" list so the agent spends their dialing time on the leads most
  likely to convert. Use when the agent says "who should I call today", "my call list", "best leads
  to call now", "prioritize my calls", or "who's worth calling". Pulls leads and recent activity from
  the CRM, ranks them by buying-signal strength, and hands back a numbered list with a one-line "why"
  and a ready opener for each.
metadata:
  version: "1.0.0"
---

# Who To Call

Phone time is the agent's most valuable hour — this skill makes sure it's spent on the right people.
It scans the pipeline, ranks who's most worth a call right now using plain buying signals, and gives
the agent a numbered list with a one-line reason and a ready-to-say opener for each. Pick up the
phone and go down the list.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent pastes/lists leads with a bit of context → rank them and write an opener for each
  ✓ Output: a numbered call list, most-worth-it first, with a one-line "why" + opener each
SUPERCHARGED (when connected)
  + ~~crm (Follow Up Boss): pull active leads, their source, stage, and recent activity to rank for real
  + ~~crm: last-touch dates flag who's gone quiet but was engaged
```

## Before you start

Load the agent's voice and business context first, then honor relevant rules:

- **Call `get_my_profile`** (on `~~crm`) first — it returns the agent's brand voice (tone, sign-off)
  and business info + rules. If it's unavailable or returns "No profile configured," fall back to
  `voice-profile.md` (voice) and `CLAUDE.md` (rules) in the working folder; if neither exists, write
  openers plainly and ask the agent for a couple of details.
- **Office hours** → only suggest calls inside them; note the best window per person where it matters.
- **No-contact days / channel rules** (e.g. "no Sundays", "email first, never cold-call") → drop or
  rephrase any call that would break a rule, or flag the timing so the agent decides.

## How the ranking works (plain signals)

Rank most-worth-it first. The strongest signals, top to bottom:

1. **Brand-new lead** still inside the agent's follow-up window (e.g. came in <1 hr ago) — speed wins.
2. **Recently active** — opened emails, clicked listings, replied, requested info, or visited a
   property page in the last day or two. They're thinking about it *now*.
3. **High-intent source** — sign call, listing inquiry, referral, or open-house sign-in beats a cold
   bought lead.
4. **Hot stage** — already touring, pre-approved, offer in motion, or near a decision.
5. **Went quiet but was engaged** — was hot, then dropped off; a call can save them before they cool.

Combine signals — a recently active hot-stage lead outranks a brand-new lead who's only just inquired.
When signals are thin, say so rather than inventing urgency.

## Output format

```markdown
# Call List | [Day, Month Date] · [N] people worth a call

1. **[Name]** — [stage / source]  ·  best time: [window or "now"]
   **Why:** [one line — the signal that puts them here]
   **Opener:** "[A ready one-liner in the agent's voice — warm, specific, ends with a question]"

2. **[Name]** — [stage / source]  ·  best time: [window]
   **Why:** [one line]
   **Opener:** "[Ready one-liner]"

3. **[Name]** — [stage / source]  ·  best time: [window]
   **Why:** [one line]
   **Opener:** "[Ready one-liner]"

*Want me to draft a voicemail or a text fallback for anyone who doesn't pick up? Just say the name.*
```

## Execution flow

1. **Load context.** Call `get_my_profile` for voice + rules (follow-up window, office hours,
   no-contact/channel rules); fall back to `voice-profile.md` and `CLAUDE.md`. Note today's day of week
   against any no-contact days.
2. **Gather leads (read-only).**
   - *Connected:* `fub_list_leads` for the active pipeline. For each candidate, `fub_get_contact` and
     `fub_get_contact_activity` to read source, stage, recent activity (opens, clicks, replies,
     showing requests), and last-touch date. Use `fub_search_contacts` if the agent named someone
     specific to include.
   - *Not connected:* ask the agent to paste or list their leads with whatever context they have
     (source, stage, last contact). Rank from that. (This is read-only — never write to the ~~crm.)
3. **Rank.** Score each lead against the plain signals above and order most-worth-it first. Keep the
   list focused — usually the top 5–10, not the whole database.
4. **Write the "why" + opener.** One honest line for why they're on the list (the real signal), then a
   short, natural opener in the agent's voice that references something specific (the listing they
   viewed, the question they asked, the timeline they gave) and ends with a question. Apply the Fair
   Housing guardrail.
5. **Respect rules in output.** Drop or rephrase any call that breaks a saved rule; flag timing
   conflicts (e.g. it's Sunday) rather than silently suggesting it.
6. **Hand off.** Offer next steps: draft a voicemail or text fallback for no-answers, run **lead-reply**
   for anyone better reached by message, or **follow-up** to set the next touch after the call.

## Fair Housing guardrail

Keep every "why" and opener about the **home, the process, and the client's stated goals** — never
neighborhood demographics or any protected class (race, religion, familial status, national origin,
disability, sex). Rank on engagement and intent signals, never on who the person is. Describe
properties and next steps; don't steer.

## Related skills

- **daily-briefing** — the full morning picture; this skill zooms in on just the call list.
- **lead-reply** — for leads better reached by message than a call.
- **follow-up** — set or draft the next touch after the call, or re-open someone who didn't answer.
