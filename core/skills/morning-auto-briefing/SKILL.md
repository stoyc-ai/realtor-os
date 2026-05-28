---
name: morning-auto-briefing
description: >
  A hands-off morning briefing that runs on its own every morning — no asking required. Use when the
  agent says "set up my morning briefing", "send me a daily briefing automatically", "schedule my
  morning briefing", "automate my daily brief", or "have my day ready when I wake up". Two jobs: help
  the agent turn it on (a daily scheduled run at a time they pick), and define what each automatic run
  produces — the prioritized briefing plus pre-drafted replies and follow-ups waiting for one-tap
  approval.
metadata:
  version: "1.0.0"
---

# Morning Auto-Briefing

This is the daily briefing on autopilot. Instead of the agent asking for it, Cowork runs it every
morning at a set time and has the whole day waiting: what matters most, today's appointments, hot
leads, who's gone quiet, tasks due — *plus* replies and follow-ups already drafted and ready to send
with one tap. The agent wakes up to a done morning, not a to-do list.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Help the agent turn this on: explain Cowork can run it daily on its own, ask for a time + what to include
  ✓ When it runs: build the prioritized briefing from whatever's available + draft the day's replies/follow-ups
SUPERCHARGED (when connected)
  + ~~crm (Follow Up Boss): today's appointments, due/overdue tasks, new + hot leads, stale contacts
  + Google Calendar: today's events with times + attendees (merged with CRM appointments)
  + Gmail: unread from active leads + threads waiting on a reply → become pre-drafted replies
```

## Two jobs

This skill does one of two things depending on when it's invoked.

**Job A — set it up (the agent is turning it on).** Triggered by "set up my morning briefing",
"automate my daily brief". The agent wants this to happen on its own each morning.

**Job B — the scheduled run (Cowork fired it automatically).** This produces the actual briefing
plus the pre-drafted outreach. See **Output format** and **Execution flow** below.

## Job A — setting it up

Explain it in plain terms, then collect two things:

1. **What this does:** "Cowork can run your morning briefing for you every day — automatically, before
   you're even up. You'll open Cowork to a ready-made plan for the day *and* replies already written
   for your new leads, so you just read and tap send."
2. **Where to turn it on:** point them to Cowork's **Scheduled** feature — "In Cowork, open the
   **Scheduled** section and add a daily run of this briefing at the time you choose. That's the one
   switch that makes this automatic."
3. **Ask two quick questions:**
   - *"What time each morning?"* (e.g. 6:30am, before showings) — this is the time they set in Scheduled.
   - *"What should it always include?"* (default: appointments, hot/new leads, stale follow-ups, tasks
     due, and pre-drafted replies — confirm or trim.)
4. Confirm the recap: "Got it — every morning at [time] you'll get [included items], with replies and
   follow-ups pre-written for approval. Set it to run daily at [time] in Cowork's **Scheduled** section
   and you're done."

Cowork triggers the run; this skill only defines what the run produces. Nothing is sent on its own —
drafts always wait for the agent.

## Before each run

Load the agent's business context and voice first, then honor relevant rules:

- **Call `get_my_profile`** (on `~~crm`) first — it returns the agent's brand voice (tone, sign-off)
  and business info + rules. If it's unavailable or returns "No profile configured," fall back to
  `voice-profile.md` and `CLAUDE.md` in the working folder.
- **Follow-up timing** (e.g. "follow up with new leads within 1 hour") → push matching leads to the top.
- **Office hours / no-contact days / channel rules** (e.g. "no Sundays", "email first, never cold-text")
  → drafts that would break a rule are still prepared, but flagged so the agent decides on timing.

## Output format

```markdown
# Good morning — here's your day | [Day, Month Date]

## #1 Priority
**[The single most important thing to do today]**
[Why it matters now + the one action to take]

## Today's Appointments
### [Time] — [Name / Address] ([Type])
**Context:** [One line] · **Prep:** [Quick action]

## Hot & New Leads
| Lead | Source | Status | Why now | Reply ready? |
|------|--------|--------|---------|--------------|
| [Name] | [Source] | [New / Hot] | [requested showing 18m ago] | ✓ drafted below |

## Stale Follow-Ups
| Contact | Last touch | Stage | Follow-up ready? |
|---------|-----------|-------|------------------|
| [Name] | [X days ago] | [Stage] | ✓ drafted below |

## Tasks Due
- [ ] [Task] — due [today / overdue N days]

---

## Ready to send (tap to approve)
> These are drafted in your voice. Nothing has been sent.

**Reply → [Name]** ([why / what they asked])
[Draft reply, in the agent's voice.]
— [sign-off]

**Follow-up → [Name]** (quiet [N] days · [stage])
[Draft follow-up referencing real context.]
— [sign-off]

*Approve any draft and I'll drop it into a Gmail draft (still never sends until you do).*
```

## Execution flow

1. **Decide the job.** Agent is turning it on / asking to automate → **Job A** (setup). Cowork fired it
   on schedule → **Job B** (the run below).
2. **Job A:** explain it plainly, point to Cowork's **Scheduled** feature for the daily time, ask for
   time + what to include, confirm the recap. Stop there — don't build a briefing yet.
3. **Job B — load context.** Call `get_my_profile` for voice + rules; fall back to `voice-profile.md`
   and `CLAUDE.md`. Note today's day of week against any no-contact days.
4. **Job B — gather data (read-only).**
   - `fub_list_appointments` (today) + **Google Calendar** (today's events) → merge and de-dupe.
   - `fub_list_tasks` → due today + overdue.
   - `fub_list_leads` → new (recently created) + hot (high score / recent activity).
   - For each flagged contact, `fub_get_contact` / `fub_get_contact_activity` → last-touch date; flag
     anyone with no recent touch as stale.
   - If Gmail is connected, surface unread from active leads + threads awaiting a reply.
5. **Job B — prioritize.** Pick the single #1 Priority (new lead inside the follow-up window → an
   appointment with a high-stakes deal → an overdue task on an active deal → the most-at-risk stale
   hot lead). The rest fill their sections.
6. **Job B — pre-draft the outreach.** Write a ready reply for each hot/new lead (grounded in what they
   asked or did) and a follow-up for each top stale contact, all in the agent's voice. Apply the Fair
   Housing guardrail. These are **drafts only** — nothing is sent.
7. **Job B — assemble.** Fill the template top-down; include only sections with content. Respect saved
   rules in the output (drop or rephrase outreach that breaks one; flag timing conflicts).
8. **On approval only.** When the agent approves a draft, if Gmail is connected create a Gmail **draft**
   (never send). Offer to log it or set the next touch in `~~crm` via `fub_add_note` / `fub_create_task`
   — both are CRM **writes**; show exactly what will be written and confirm first.

## Write safety

- The scheduled run is **read-only** except for the drafts it prepares — and those are **not sent**.
- Creating a Gmail draft, logging a note (`fub_add_note`), or setting a task (`fub_create_task`) happens
  **only after the agent approves**. Show what will be written and confirm first.

## Fair Housing guardrail

Keep every pre-drafted reply and follow-up about the **home, the process, and the client's stated
goals** — never neighborhood demographics or any protected class (race, religion, familial status,
national origin, disability, sex). Describe properties and next steps; don't steer.

## Related skills

- **daily-briefing** — the same briefing on demand, when the agent asks for it mid-day.
- **lead-reply** — full reply to a single inbound lead; this skill pre-drafts those in bulk.
- **follow-up** — re-open a quiet thread; this skill queues the top ones automatically each morning.
- **who-to-call** — turn the morning's hot leads into a ranked call list.
