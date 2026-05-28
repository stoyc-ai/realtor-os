---
name: call-recap
description: >
  Turn messy call or showing notes into a clean CRM log plus next steps, fast. Use when the agent
  says "log my call", "recap this showing", "log this conversation with [name]", "I just got off the
  phone with [name]", "note this for [name]", or dictates/pastes what just happened on a call. Drafts
  a structured recap, then — only after the agent confirms — writes the note, call log, and follow-up
  tasks to the CRM.
metadata:
  version: "1.0.0"
---

# Call Recap

Right after a call or showing, the agent dumps what happened — half-sentences, names, numbers — and
this skill turns it into a clean, structured CRM entry with concrete next steps. It **drafts first,
shows everything, and writes to the CRM only on explicit confirmation.** Nothing is ever logged
silently.

## How it works

```
ALWAYS (works standalone)
  ✓ Agent dictates or pastes what happened on the call/showing
  ✓ Output: a structured recap (summary, key points, sentiment, next steps) to copy-paste anywhere
SUPERCHARGED (when connected)
  + ~~crm: resolve the contact (fub_search_contacts / fub_get_contact) and pull recent context
  + ~~crm: write the recap as a note, log the call, create dated follow-up tasks, update stage/tags
    — each as a confirmed write, never automatic
```

## Before you start

Read the agent's `CLAUDE.md` (saved by **remember**) and honor relevant rules — follow-up cadence
(e.g. *"follow up within 1 hour"*, *"book the next touch within 24h"*), no-contact days, and how
they label stages/tags. Use these to set sensible due dates on tasks and to phrase the recap.

If a working folder / `CLAUDE.md` isn't present, proceed with sensible defaults.

## Output format

Always produce the recap first, in this shape:

```markdown
# Call Recap — [Name] · [Showing / Call / Listing appt] · [Date]

**Summary:** [1–2 lines: who, about what, and the headline outcome.]

**Key Points**
- [Fact, number, or preference that matters — e.g. budget, timeline, must-haves]
- [Objection or concern raised]
- [Anything promised: "I'll send comps for 4th St"]

**Sentiment / Stage signal:** [e.g. Warm — ready to tour again; or Cooling — price-sensitive,
stalling. Note any stage change you'd suggest, e.g. New Lead → Active.]

**Next Steps**
- [ ] [Concrete action] — by [date]
- [ ] [Concrete action] — by [date]
```

Then, only if the CRM is connected, show the **Proposed CRM updates** block (do not write yet):

```markdown
## Proposed CRM updates — [Name]

1. **Add note** (fub_add_note): the recap above, saved to their record.
2. **Log call** (fub_log_call): [outbound/inbound] call · [duration if known] · outcome: [one line].
3. **Create task** (fub_create_task): "[task]" — due [date].
   **Create task** (fub_create_task): "[task]" — due [date].
4. **Update lead** (fub_update_lead): stage [old → new] / add tag "[tag]"  ← only if it changed.

Apply these to [Name]'s record?
```

Only include the lines that actually apply. If nothing changed (no new task, no stage move), don't
invent items.

## Execution flow

1. **Capture.** Take the agent's dictation/paste. If it's thin, ask one quick clarifying question
   (who, and the one outcome that matters) — otherwise work with what's given.
2. **Resolve the contact** (connected): `fub_search_contacts` on the name → `fub_get_contact` to load
   their current stage, tags, and recent history so the recap and any stage change are accurate. If
   the search returns multiple matches, ask the agent which one before going further. *Not connected:*
   skip straight to the recap and tell the agent to paste it into their CRM.
3. **Draft the recap** using the **Output format** — Summary, Key Points, Sentiment/Stage signal, and
   Next Steps as concrete, dated actions (apply `CLAUDE.md` cadence for due dates). Apply the Fair
   Housing guardrail.
4. **Build the Proposed CRM updates** list (connected only): the note, an optional call log, follow-up
   task(s) with due dates, and a stage/tag update **only if** the conversation actually moved them.
5. **Show everything and ask "Apply these to [Name]'s record?"** Let the agent edit the recap, drop a
   task, or change a due date first. **Write nothing until they confirm.**
6. **Write only confirmed items, confirming before each distinct write:**
   - `fub_add_note` — the recap as a note.
   - `fub_log_call` — the call entry (only if logging a call).
   - `fub_create_task` — one call per follow-up task, with its due date.
   - `fub_update_lead` — stage/tag change (only if it changed).
   If the agent approved the whole block at once, you may proceed through them in order — but if any
   single item is risky or ambiguous (e.g. a stage change), re-confirm that one specifically.
7. **Report what was written:** list each action taken (and anything skipped), e.g. *"Logged: note +
   call + 1 task (due Fri). Skipped stage change."*

## Write safety

- **Draft before write, always.** Never call a write tool before the agent has seen the recap and the
  Proposed CRM updates and said yes.
- **Confirm before each distinct write.** Adding a note, logging a call, creating a task, and updating
  a lead are four separate writes — surface them as such; re-confirm any single one that's ambiguous
  (especially `fub_update_lead` stage moves, which are easy to get wrong).
- **No silent edits.** If the agent says "just give me the recap," produce the text only and write
  nothing.
- **Echo back.** After writing, state exactly what landed in the CRM and what you left out.

## Fair Housing guardrail

Keep the recap about the **home, the deal, and the client's stated goals** — budget, timeline,
must-haves, objections. Never log notes about a client's or neighborhood's protected
characteristics (race, religion, familial status, national origin, disability, sex). Describe
properties and next steps, not demographics.

## Related skills

- **follow-up** — the next-step tasks here often become a scheduled follow-up; hand them off to draft
  the actual message.
- **lead-reply** — if the call ended with "I'll email you that", draft the reply in the agent's voice.
- **daily-briefing** — the tasks logged here surface in tomorrow's plan as due/overdue items.
