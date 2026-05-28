---
name: lead-reply
description: >
  Draft a reply to a lead or contact in the agent's own voice, informed by CRM context. Use when
  the agent says "reply to [name]", "respond to this lead", "draft a reply to [name]", "what should
  I say to [name]", or "answer this lead". Pulls the contact's recent activity and the email thread,
  then produces a ready-to-send draft (never sends) plus a couple of alternative angles.
metadata:
  version: "1.0.0"
---

# Lead Reply

Turn an incoming lead message into a ready-to-send reply that sounds like *the agent* — not generic
AI — and reflects what the CRM already knows about that person. This is a **draft-only** skill: it
writes the reply and hands it back. It never sends anything, and it only logs the outreach or sets a
follow-up task **after the agent says yes**.

## How it works

```
ALWAYS (works standalone)
  ✓ Agent pastes the lead's message (and the lead's name if they have it)
  ✓ Loads voice-profile.md + CLAUDE.md so the draft sounds like them and follows their rules
  ✓ Output: a ready-to-send draft + 1–2 alternative angles
SUPERCHARGED (when connected)
  + ~~crm: find the contact, pull recent activity/context (last touch, stage, source, notes)
  + Gmail: pull the actual email thread so the reply matches what was already said
```

## Load first (every time)

1. **Load the agent's voice.** Call the `get_my_profile` tool first — it returns the agent's brand
   voice (tone, writing examples, sign-off, hashtags) + business info (brokerage, service areas,
   niche, rules). Use it as the voice/style source. If it's unavailable or returns "No profile
   configured," fall back to reading `voice-profile.md` and `CLAUDE.md` from the working folder; if
   neither exists, ask the agent.

If you're falling back to the saved files in the **selected working folder**:

- **`voice-profile.md`** (from **learn-my-voice**) — write the reply in this voice: tone, rhythm,
  emoji/hashtag habits, and especially the few-shot examples. If it's missing, draft in a warm,
  plain-spoken default and suggest running **learn-my-voice** so future replies sound like them.
- **`CLAUDE.md`** (from **remember**) — honor operating rules: response-time expectations, email
  sign-off, business facts (brokerage, service area, hours), and any "don't" rules (e.g. *don't
  cold-text leads*). The sign-off in the draft comes from here.

## Fair Housing guardrail

Describe the **home and its features** — never the **buyer's demographics** or a neighborhood's
makeup. No steering language: don't reference or imply race, color, religion, national origin, sex,
familial status, or disability, and don't characterize an area by who lives there ("good schools for
families like yours", "safe neighborhood", "great for a young couple"). Answer the lead's actual
question about the property, price, process, or timing. If the lead's message asks for steering
("is this a good area for people like me?"), redirect to objective facts and offer to share data.

## Gather context

```
1. Identify the person.
   - Name given → fub_search_contacts to find them → fub_get_contact for details.
   - Email/phone given → fub_search_contacts on that identifier.
   - Ambiguous match → list the candidates and ask which one.
2. Pull recent context → fub_get_contact_activity
   (last contact, stage/status, lead source, property interest, prior notes).
3. Pull the thread → Gmail: find the most recent email from/to this lead so the reply
   continues the real conversation instead of restarting it.
4. Nothing connected (or contact not found)?
   → Ask the agent to paste the lead's message and any context they have, then draft from that.
```

Never invent facts about the lead or the property. If a key detail is unknown (price, availability,
showing time), leave a clearly marked `[fill in: …]` placeholder rather than guessing.

## Output format

```markdown
# Reply to [Lead Name]
**Context:** [stage / source / last touch — or "pasted message only"]

---

## Draft (ready to send)

**To:** [lead email, if known]
**Subject:** [only if email — match or continue the thread subject]

[Body — written in the agent's voice, in plain text (no markdown/asterisks so it renders
clean in any client). Short paragraphs. Answers their actual question, one clear next step.]

[Sign-off from CLAUDE.md]

---

## Alternative angles

**More direct:** [2–3 line version that pushes for the appointment/next step sooner]
**Softer:** [2–3 line version that's lower-pressure, nurtures, gives them room]

---

**Next step?** I can (a) log this outreach to [Lead Name] in ~~crm, and/or (b) set a follow-up
task. Just say the word — I won't write anything until you confirm.
```

If there's no email (text/DM lead), drop the subject line and write it as a short message in the
agent's texting voice — still honoring any "don't cold-text" rule in `CLAUDE.md`.

## Execution flow

1. **Parse the request** — get the lead's name (or the pasted message). "reply to Sarah", "what
   should I say to this lead?", "answer this", etc.
2. **Load voice + rules** — call `get_my_profile` first; fall back to `voice-profile.md` and
   `CLAUDE.md` from the working folder if it's unavailable or unconfigured.
3. **Gather context** — find the contact (`fub_search_contacts` → `fub_get_contact`), pull activity
   (`fub_get_contact_activity`), and the Gmail thread. If nothing's connected, ask them to paste the
   message.
4. **Draft in their voice** — answer the lead's real question, mirror the few-shot examples, keep it
   tight, end with one clear next step, and close with their sign-off. Apply the Fair Housing
   guardrail throughout.
5. **Add 2 alternatives** — one more direct, one softer, so the agent can pick the energy.
6. **Output the draft** using the template. Do **not** send.
7. **Offer the next step** — ask whether to (a) log the outreach (`fub_add_note`) and/or (b) create a
   follow-up task (`fub_create_task`). Only call those tools **after the agent explicitly confirms**,
   then confirm in one line what was written and where.

## Write safety

- This skill **drafts only** — it never sends an email or message.
- `fub_add_note` and `fub_create_task` are the only writes, and they fire **only on explicit
  confirmation**. If the agent doesn't respond to "Next step?", write nothing.
- When logging outreach, store a brief factual note (date, channel, summary) — never the buyer's
  protected characteristics.

## Related skills

- **follow-up** — for nurturing leads who've gone quiet or are on a cadence (not a direct reply).
- **call-recap** — turn a call into notes + next actions; pairs with logging the outreach here.
- **learn-my-voice** — builds/updates the `voice-profile.md` this skill writes from. If a draft
  "doesn't sound like me", run that skill to refine the voice.
