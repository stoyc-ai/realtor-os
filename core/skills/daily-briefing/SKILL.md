---
name: daily-briefing
description: >
  A prioritized morning briefing so the agent knows exactly what matters today. Use when the agent
  says "start my day", "morning briefing", "daily brief", "what's on my plate today", "what do I
  need to do today", or "prep my day". Pulls today's appointments, due/overdue tasks, new and hot
  leads, and flags stale contacts — then ranks them into a single clear action plan.
metadata:
  version: "1.0.0"
---

# Daily Briefing

Open the day with one scannable, prioritized view: the single most important thing, today's
appointments with quick context, new and hot leads, who needs a follow-up, and what's due. Works
from whatever the agent gives you and gets sharper when the CRM, calendar, and email are connected.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent pastes/lists: today's appointments, deals in motion, anything urgent
  ✓ Output: a prioritized, 2-minute briefing with a clear #1 Priority + top 3 actions
SUPERCHARGED (when connected)
  + ~~crm (Follow Up Boss): today's appointments, due/overdue tasks, new + hot leads, stale contacts
  + Google Calendar: today's events with times + attendees (merge with CRM appointments)
  + Gmail: unread from active leads, threads waiting on a reply
```

## Before you start

Load the agent's business context first, then honor relevant rules:

- **Call `get_my_profile`** (on `~~crm`) first — it returns the agent's business info and rules. If it's unavailable or returns "No profile configured," fall back to the agent's `CLAUDE.md` (saved by the **remember** skill) in the working folder.
- **Follow-up timing** (e.g. "follow up with new leads within 1 hour") → escalate matching leads into #1 Priority or Suggested Actions.
- **Office hours** → frame the day within them; don't suggest contacting before/after.
- **No-contact days / channel rules** (e.g. "no Sundays", "email first, never cold-text") → suppress or rephrase any suggested outreach that would violate them.

If neither a profile nor a `CLAUDE.md` exists, proceed with sensible defaults and offer to save rules later via **remember**.

## Output format

```markdown
# Daily Briefing | [Day, Month Date]

## #1 Priority
**[The single most important thing to do today]**
[Why it matters now + the one action to take]

## Today's Appointments
### [Time] — [Name / Address] ([Type: showing, listing pres, closing, call])
**Context:** [One line — buyer/seller, stage, last touch, what's at stake]
**Prep:** [Quick action before this — confirm time, pull comps, print docs]

### [Time] — [Name / Address] ([Type])
**Context:** [One line]
**Prep:** [Quick action]

*Run `appointment-prep [name or address]` for full prep on any of these.*

## New & Hot Leads
| Lead | Source | Status | Why now | Action |
|------|--------|--------|---------|--------|
| [Name] | [Source] | [New / Hot] | [e.g. requested showing 18m ago] | [Call / reply] |

## Needs Follow-Up
| Contact | Last touch | Stage | Suggested move |
|---------|-----------|-------|----------------|
| [Name] | [X days ago] | [Stage] | [Check in / send listing] |

## Tasks Due
- [ ] [Task] — due [today / overdue N days]
- [ ] [Task] — due [today]

## Suggested Actions
1. **[Action]** — [why now]
2. **[Action]** — [why now]
3. **[Action]** — [why now]
```

## Execution flow

1. **Load business context.** Call `get_my_profile` first — it returns the agent's business info and rules (follow-up timing, office hours, no-contact/channel rules). If it's unavailable or returns "No profile configured," fall back to `CLAUDE.md` in the working folder. Note today's day of week against any no-contact days.
2. **Gather data.**
   - *Connected:* `fub_list_appointments` (today) and **Google Calendar** (today's events) → merge and de-dupe. `fub_list_tasks` → due today + overdue. `fub_list_leads` → new (created recently) + hot (high score / recent activity). For each flagged lead/contact, use `fub_get_contact` / `fub_get_contact_activity` to find last-touch date and flag anyone with no recent touch (stale). If Gmail is connected, surface unread from active leads and threads awaiting a reply.
   - *Not connected:* ask the agent to paste or list today's appointments, the deals they're focused on, and anything urgent. Build the briefing from that. (This is read-only — never write to the ~~crm.)
3. **Prioritize.** Rank into a single #1 Priority using roughly:
   1. A new lead inside the agent's follow-up window (e.g. <1 hr) → contact now.
   2. An appointment today with a high-stakes deal (listing pres, closing, offer in play).
   3. An overdue task tied to an active deal, or a time-sensitive reply from a buyer/seller.
   4. The most-at-risk stale contact (hot lead gone quiet, no touch in too long).
   Pick the top item as #1; the rest feed their sections.
4. **Assemble.** Fill the template top-down. Include only sections with content (always include #1 Priority and Suggested Actions). Keep each appointment to one context line + one prep action, and point to **appointment-prep** for depth.
5. **Respect rules in output.** Drop or rephrase any suggested outreach that breaks a saved rule (no-contact day, channel preference, outside office hours).
6. **Hand off.** Offer next steps: run **appointment-prep** before a meeting, **follow-up** for stale contacts, **lead-reply** for new/hot leads.

## Quick brief

When the agent says "quick brief" or "tldr my day", skip the full layout and return:

```markdown
# Quick Brief | [Date]

**#1:** [The single most important action]
**Appointments:** [N] — [Name/Addr 1], [Name/Addr 2], [Name/Addr 3]
**Hot/new leads:** [N] — [top one + why now]
**Tasks due:** [N] ([M] overdue)
**Do now:** [Single most important action]
```

## Related skills

- **appointment-prep** — full prep for any specific appointment in the briefing.
- **follow-up** — draft check-ins for the stale contacts flagged here.
- **lead-reply** — respond to the new and hot leads surfaced here.
- Reads rules/facts saved by **remember** (`CLAUDE.md`); writes nothing.
