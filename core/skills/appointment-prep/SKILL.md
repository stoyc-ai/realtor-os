---
name: appointment-prep
description: >
  Get the agent fully prepared for any upcoming appointment in minutes. Use when the agent says
  "prep me for my appointment with [name]", "showing prep for [address]", "get me ready for my
  listing appointment", "prep my meeting with [name]", or "buyer consult prep". Pulls relationship
  history from the CRM, finds the meeting on the calendar, and runs light web research on the
  property, neighborhood, and person — then hands back a ready-to-walk-in prep brief.
metadata:
  version: "1.0.0"
---

# Appointment Prep

Walk into any appointment knowing the person, the property, and exactly what to say. This skill works
with just a name and meeting type, and gets sharper when the CRM and calendar are connected. It is
**read-only** — it never writes to the CRM or calendar.

Real estate appointment types this covers: **listing appointment, buyer consult, showing, open
house, closing.** Read `CLAUDE.md` first for the agent's market, brokerage, niche, and rules.

## How it works

```
ALWAYS (works standalone)
  ✓ Agent gives a name and/or address + the meeting type
  ✓ Web search: property facts, neighborhood context, public info on the person
  ✓ Output: a prep brief — history, context, talking points, questions, objections, logistics
SUPERCHARGED (when connected)
  + ~~crm: relationship history, last touches, open items, prior appointments
  + Google Calendar: auto-find the meeting, pull time/location/attendees/notes
```

## Inputs to gather

If the agent didn't say, ask for the two things the brief needs most:

- **Meeting type** — listing appointment, buyer consult, showing, open house, or closing
- **Address** — the subject property (skip for a buyer consult with no property yet)

A name is enough to find the rest. If `~~crm` and Google Calendar are connected, pull the time,
location, and contact automatically and skip the questions.

## Research rules (important)

This skill uses **web search** for the property, neighborhood, and person. Treat external facts
carefully:

- **Cite every external fact** — link or name the source inline.
- **Tag recency** — e.g. *(listing pulled May 2026)*, *(article dated Apr 2026)*. Flag anything that
  looks stale.
- **Never fabricate property facts.** If beds/baths, square footage, lot size, price, or sale history
  can't be verified, write *"unverified — confirm on-site / in MLS"* rather than guessing.
- **Public info only** on the person. No private data; keep it to what helps the conversation.
- **Fair Housing:** describe the **home and features**, never buyer or neighborhood demographics.

## Output format

```markdown
# Appointment Prep: [Name] — [Meeting Type]

**Who:** [Name(s) + role: seller / buyer / both agents]
**When:** [Date/time, from calendar if known]
**Type:** [Listing appt / Buyer consult / Showing / Open house / Closing]
**Address:** [Subject property, or "buyer consult — no property yet"]

---

## Relationship & History
- **Source / how they came in:** [lead source]
- **Last touches:** [most recent 3–5 interactions, dated]
- **Open items:** [unanswered questions, promised follow-ups, pending tasks]
- **Prior appointments:** [past meetings with this contact, outcomes]
- **Notes that matter:** [budget, timeline, motivation, must-haves]

## Property & Neighborhood Context
*(All facts cited and recency-tagged. Unverified items flagged.)*
- **The property:** [beds/baths, sq ft, lot, year built — each cited] *(source, date)*
- **Pricing context:** [list/last-sale/estimate — cited] *(source, date)*
- **Comps / market:** [recent nearby sales, days on market, trend] *(source, date)*
- **Neighborhood:** [schools, amenities, what's selling] *(source, date)*
- **Unverified — confirm on-site/in MLS:** [anything not nailed down]

## Talking Points
- [3–5 specific, personalized things to lead with — tie to history + research]

## Smart Questions to Ask
1. [Question that surfaces motivation / timeline]
2. [Question about decision process or other stakeholders]
3. [Question specific to this meeting type]
4. [Question that fills a gap in what we know]

## Likely Objections + Responses
| Objection | Suggested Response |
|-----------|-------------------|
| [Common objection for this stage/type] | [How to handle it] |
| [Objection hinted at in history] | [How to handle it] |

## Logistics
- **Time / location:** [confirmed details, parking, lockbox/access notes]
- **Bring:** [CMA, listing presentation, disclosures, business cards, etc.]
- **Drive time / leave by:** [if known]

## Do this before the meeting
**→ [One concrete action]** — e.g. "Confirm the appointment by text," "Pull MLS comps for 123 Main,"
"Re-read their last email about the kitchen reno."
```

## Execution flow

1. **Identify the appointment.**
   - If Google Calendar is connected, find the matching upcoming event; pull time, location,
     attendees, and any description/notes.
   - If `~~crm` is connected, run `fub_list_appointments` to confirm the booked appointment and type.
   - If neither resolves it, ask the agent for meeting type + address.
2. **Pull the contact + history.**
   - `fub_search_contacts` to locate the person by name (confirm if multiple match).
   - `fub_get_contact` for profile, source, budget, timeline, tags.
   - `fub_get_contact_activity` for the last touches, prior appointments, and open items.
   - If `~~crm` isn't connected, ask the agent to paste what they have.
3. **Research the property & neighborhood (web search).** Look up the subject address (public listing,
   recent sale, comps, schools, neighborhood). Cite sources and tag recency; flag anything unverified.
   Skip property research for a buyer consult with no property yet.
4. **Research the person (web search, public info only).** Light check for context that helps the
   conversation (profession, ties to the area). Cite sources; no private data.
5. **Tailor to the meeting type:**
   - **Listing appointment** — pricing/comps, seller motivation, marketing plan, commission objections.
   - **Buyer consult** — needs/wants, budget + financing, timeline, buyer-rep agreement, process.
   - **Showing** — property details, comps, why-this-home talking points, next-step (offer) questions.
   - **Open house** — neighborhood pitch, traffic/sign-in plan, likely walk-in questions.
   - **Closing** — final-walkthrough items, docs, funds, possession, post-close follow-up.
6. **Synthesize the brief** using the output template. Combine CRM history + research into talking
   points, smart questions, and likely objections. End with one clear "do this before the meeting"
   action.
7. **Deliver** the brief and offer to refine ("want comps for a different radius?" / "add a fifth
   question?"). Do not write anything back to the CRM or calendar.

## Related skills

- **daily-briefing** — see the full day, including which appointments to prep next.
- **contact-research** — deeper dive on a single person or household before first contact.
- **call-recap** — after the meeting, capture what happened and the follow-ups.
