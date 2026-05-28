---
name: follow-up
description: >
  Keep the agent's pipeline warm by drafting follow-ups and surfacing who's gone cold. Use when the
  agent says "follow up with [name]", "draft a follow-up", "who do I need to follow up with",
  "write a follow-up sequence", "I haven't heard back from [name]", or "anyone I'm forgetting".
  Pulls real contact history from the CRM and writes the nudge in the agent's own voice.
metadata:
  version: "1.0.0"
---

# Follow-Up

Nobody falls through the cracks. This skill does two jobs: draft a contextual follow-up to one
person, or sweep the pipeline to surface every contact who's overdue for a touch — then draft the
ones the agent picks. Every message sounds like the agent and respects their follow-up rules.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent names a contact + gives context → draft a follow-up in their voice
  ✓ Agent pastes a list of stale contacts → rank them and draft nudges
SUPERCHARGED (when connected)
  + ~~crm: pull full history, last-touch dates, and stage so drafts are specific
  + ~~crm: sweep fub_list_leads + activity to find who's gone quiet automatically
  + Gmail: drop the approved draft straight into a Gmail draft (never sends)
```

## Before drafting (always)

1. **Load the agent's voice.** Call the `get_my_profile` tool first — it returns the agent's brand
   voice (tone, writing examples, sign-off, hashtags) + business info (brokerage, service areas,
   niche, rules). Use it as the voice/style source. If it's unavailable or returns "No profile
   configured," fall back to reading `voice-profile.md` and `CLAUDE.md` from the working folder; if
   neither exists, ask the agent. When falling back: `voice-profile.md` (from **learn-my-voice**)
   makes the message sound like the agent — if it's missing, write plainly and suggest running
   **learn-my-voice**.
2. Honor follow-up rules and cadence — e.g. *"follow up within 1 hour"*, *"no contact on Sundays"*,
   *"email first, never cold-text"*, sign-off — from the profile (or `CLAUDE.md` on fallback).
   **Honor these.** If a rule would block a send (e.g. it's Sunday and the agent said no Sunday
   contact), draft it anyway and flag the timing.

## Mode A — single follow-up

Triggered by *"follow up with Maria"*, *"I haven't heard back from the Patels"*.

1. Resolve the contact: `fub_search_contacts` → `fub_get_contact` → `fub_get_contact_activity` to
   see last touch, stage, properties of interest, and what was last discussed.
2. Write **one** contextual nudge that references something real (the listing they toured, the
   question they asked, the timeline they mentioned) — not a generic "just checking in".
3. Offer an optional **3-touch sequence** (day 1 / day 3 / day 7) if they've gone quiet and the
   agent wants a fuller cadence.

## Mode B — sweep ("who do I need to follow up with")

1. `fub_list_leads` to get the active pipeline.
2. For each, check `fub_get_contact_activity` for the last meaningful touch.
3. Flag anyone past the cadence in `CLAUDE.md` (default: **no touch in 7+ days** for active leads,
   **14+ days** for nurture). Rank by stage and how cold they've gone — hottest leads gone quiet
   surface first.
4. Present the table below. The agent picks who to draft; **do not draft all of them** unprompted.

## Output format

**Single follow-up:**

```
Follow-up — [Name]  ·  Stage: [stage]  ·  Last touch: [date / what happened]

[The draft message, in the agent's voice, referencing real context.]

— [sign-off from CLAUDE.md]
```

**Optional 3-touch sequence** (offer, don't force):

```
Day 1 — [short, warm re-open]
Day 3 — [add value: a comp, a new listing, an article]
Day 7 — [soft close / easy out: "want me to keep you posted or pause?"]
```

**Sweep:**

```
Pipeline sweep — [N] contacts overdue for a follow-up

| Contact      | Last touch    | Stage         | Suggested action                 |
|--------------|---------------|---------------|----------------------------------|
| Maria Lopez  | 9 days ago    | Hot — toured  | Nudge re: 4th St offer deadline  |
| The Patels   | 16 days ago   | Nurture       | Send new listings in their range |
| Dan Cho      | 22 days ago   | Cold lead     | Soft re-engage / confirm active  |

Reply with names (or "draft all") and I'll write each follow-up.
```

## Execution flow

1. Determine mode: a named contact → **Mode A**; "who do I need to follow up with" → **Mode B**.
2. Call `get_my_profile` for voice + rules (cadence, do/don't rules, sign-off); fall back to
   `voice-profile.md` and `CLAUDE.md` if it's unavailable or unconfigured.
3. **Mode A:** resolve contact and pull history via `fub_search_contacts` /
   `fub_get_contact` / `fub_get_contact_activity`. **Mode B:** `fub_list_leads` +
   `fub_get_contact_activity` per lead, then rank against the cadence rule.
4. Draft using the **Output format**, grounded in real history. Apply the Fair Housing guardrail.
5. Present to the agent. Revise on feedback until they approve.
6. **On approval only:** if Gmail is connected, create a Gmail **draft** (never send). Offer to log
   it in `~~crm` — `fub_add_note` (record the outreach) and/or `fub_create_task` (set the next
   touch, e.g. "follow up again Day 3"). These are CRM **writes** — confirm each before calling.

## Write safety

- This skill **drafts only**. It never sends email and never auto-texts.
- Creating a task/reminder (`fub_create_task`) or logging a note (`fub_add_note`) is a CRM write.
  Show the agent exactly what will be written and get explicit confirmation first.
- Respect blocking rules in `CLAUDE.md` (e.g. no Sunday contact, email-before-text). When a rule
  affects timing, draft anyway and flag it — let the agent decide.

## Fair Housing guardrail

Keep follow-ups about the **home, process, and the client's stated goals** — never about
neighborhood demographics or any protected class (race, religion, familial status, national origin,
disability, sex). Don't steer; describe properties and next steps.

## Related skills

- **lead-reply** — responds to an inbound message; **follow-up** re-opens a quiet thread.
- **daily-briefing** — surfaces today's priorities, including who's due for a follow-up.
- **call-recap** — after a call, capture next steps that often become a scheduled follow-up.
