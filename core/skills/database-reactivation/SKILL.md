---
name: database-reactivation
description: >
  Wake up the cold, forgotten contacts sitting in the agent's CRM and turn them back into
  conversations. Use when the agent says "reactivate my database", "find my cold leads", "re-engage
  old leads", "who haven't I talked to in a while", or "warm up my old leads". Scans the CRM for
  contacts with no recent touch, groups them into simple segments, and drafts a value-first
  re-engagement message per segment in the agent's voice — with a suggested cadence.
metadata:
  version: "1.0.0"
---

# Database Reactivation

Most agents are sitting on a goldmine of old leads they've forgotten — people who once raised a hand
and went quiet. This skill finds them, sorts them into simple groups (old buyer leads, past sellers,
people who inquired but never booked), and drafts one warm, value-first message per group in the
agent's voice. The goal isn't a hard sell — it's to re-open the door so the right ones reply.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent pastes/lists old contacts (with type + last contact) → segment them and draft a message per segment
  ✓ Output: who's gone cold + a value-first re-engagement draft per segment + a suggested cadence
SUPERCHARGED (when connected)
  + ~~crm (Follow Up Boss): scan the database for contacts with no recent touch automatically
  + ~~crm: source, stage, and history sort each contact into the right segment and ground each draft
```

## Before you start

Load the agent's voice and business context first, then honor relevant rules:

- **Call `get_my_profile`** (on `~~crm`) first — it returns the agent's brand voice (tone, writing
  examples, sign-off) and business info + rules. If it's unavailable or returns "No profile
  configured," fall back to `voice-profile.md` (voice) and `CLAUDE.md` (rules) in the working folder.
  If `voice-profile.md` is missing, write plainly and suggest running **learn-my-voice**.
- **What counts as "cold"** → default to **no meaningful touch in 60+ days** unless the agent's rules
  say otherwise. Ask the agent to confirm or change the window if they want.
- **Channel rules / no-contact days** (e.g. "email first, never cold-text", "no Sundays") → draft in
  the allowed channel; flag any timing conflict rather than breaking a rule.

## Simple segments

Group cold contacts into a few plain buckets so each message fits the person:

- **Old buyer leads** — once looking to buy, never closed. Lead with new inventory or rate changes.
- **Past sellers / past clients** — already did a deal. Lead with their home's current value or a
  check-in; these are the warmest and often refer.
- **Inquired but never booked** — asked about a listing or a valuation and ghosted. Lead with the
  thing they originally wanted ("you asked about [area] last year…").
- **General nurture / unknown** — old contacts with thin history. Lead with a low-pressure value offer.

Use only the segments that actually have people in them.

## Output format

```markdown
# Database Reactivation Plan | [Date]

**[N] contacts have gone cold** (no touch in [window]). Here's who, grouped, and a draft for each group.

## Who's gone cold
| Contact | Last touch | Segment |
|---------|-----------|---------|
| [Name]  | [date / N months ago] | Old buyer lead |
| [Name]  | [date] | Past seller |
| [Name]  | [date] | Inquired, never booked |

## Draft messages (one per segment)

**Old buyer leads** ([N] people)
[Short, value-first message in the agent's voice — references what they wanted, offers something
useful, soft question to re-open. Not salesy.]
— [sign-off]

**Past sellers / clients** ([N] people)
[Warm check-in + value (home-value update, market note). Easy reply.]
— [sign-off]

**Inquired, never booked** ([N] people)
[Re-open the original interest, low pressure.]
— [sign-off]

## Suggested next step
- **Cadence:** [e.g. send the segment message → if no reply in 5–7 days, one soft nudge → then rest 30 days]
- **Where to start:** [the warmest segment first — usually past clients]
- Want me to set this up in your CRM (tasks/reminders per contact)? I'll show you the exact plan first.
```

## Execution flow

1. **Load context.** Call `get_my_profile` for voice + rules (cold window, channel rules, sign-off);
   fall back to `voice-profile.md` and `CLAUDE.md`. Confirm the "cold" window with the agent if unsure.
2. **Find cold contacts (read-only).**
   - *Connected:* `fub_list_leads` for the database, then `fub_get_contact` / `fub_get_contact_activity`
     per contact to read source, stage, and last meaningful touch. Flag everyone past the cold window.
     Use `fub_search_contacts` if the agent points to a specific group.
   - *Not connected:* ask the agent to paste or list old contacts with type and rough last-contact date.
     Build the plan from that. (This is read-only — never write to the ~~crm.)
3. **Segment.** Sort the cold contacts into the simple buckets above; keep only buckets with people.
4. **Draft one message per segment.** Value-first, in the agent's voice, grounded in what that group
   originally wanted — never a generic blast. Apply the Fair Housing guardrail. Keep it short and easy
   to reply to.
5. **Suggest a cadence.** A simple, light sequence (initial message → one soft nudge if no reply →
   long rest), warmest segment first. Nothing automated without approval.
6. **Present and confirm before any write.** Show the table, drafts, and cadence. If the agent wants
   Cowork to set up tasks or reminders per contact, that's a CRM **write** — lay out exactly what will
   be created (`fub_create_task` per contact, `fub_add_note` to log the outreach) and get explicit
   confirmation before calling anything. Drafting and segmenting are always free; writing never happens
   on its own.

## Write safety

- Scanning, segmenting, and drafting are **read-only** and create nothing in the CRM.
- Setting up tasks/reminders (`fub_create_task`) or logging notes (`fub_add_note`) — i.e. "enrolling"
  contacts into the cadence — is a CRM **write**. Show the exact plan (which contacts, what tasks) and
  confirm first. Never enroll the whole database in one click without the agent saying yes.

## Fair Housing guardrail

Keep every segment and message about the **home, the market, the process, and the contact's stated
goals** — never neighborhood demographics or any protected class (race, religion, familial status,
national origin, disability, sex). Segment on behavior and history (buyer vs. seller, last touch),
never on who people are. Describe value and next steps; don't steer.

## Related skills

- **follow-up** — for re-opening one specific quiet contact; this skill works the whole cold database.
- **who-to-call** — turn the warmest reactivation replies into a ranked call list.
- **lead-reply** — respond when a reactivated contact writes back.
