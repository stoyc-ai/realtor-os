---
name: contact-research
description: >
  Build a quick, useful picture of a lead or contact before the agent reaches out or meets. Use when
  the agent says "research [name]", "what do we know about [name]", "look up this lead", "background
  on [name]", "tell me about [name] before I call", or "who is this person". Combines the CRM record
  with cited public web background into a short, ready-to-use dossier.
metadata:
  version: "1.0.0"
---

# Contact Research

Walk into the call or showing already knowing who you're talking to. This skill pulls the contact's
internal CRM history and pairs it with **cited, dated** public background, then hands back a concise
dossier with genuine conversation hooks and a clear next step. **Read-only** — it never writes to the
CRM or anywhere else.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

Call `get_my_profile` (on `~~crm`) first for the agent's business facts and rules (service area,
niche, do/don'ts); if it's unavailable or returns "No profile configured," fall back to `CLAUDE.md`.

## How it works

```
ALWAYS (works standalone)
  ✓ Agent gives a name or email
  ✓ Output: a dossier built from public web background (cited + dated) + whatever the agent tells you
SUPERCHARGED (when connected)
  + ~~crm connected: pull the real CRM snapshot + recent activity (fub_get_contact, fub_get_contact_activity)
  + Claude web search: public real-estate history, employer/role, public social presence, neighborhood
```

Even standalone, **always** run a web search for public background — it's the core of the dossier.
The CRM just makes the internal half real instead of agent-recalled.

## Ground rules (read before researching)

- **Cite everything from the web.** Every external fact gets a source and a recency tag, e.g.
  *(LinkedIn, accessed May 2026)* or *(county records, 2023)*. No citation → don't state it.
- **Never fabricate.** If you can't verify something, say so. Mark anything shaky as *unverified*.
- **Public info only.** Use what's openly published. Don't dig for private/sensitive details, and
  don't infer them.
- **Fair Housing.** Never profile or speculate about protected classes (race, religion, familial
  status, national origin, disability, sex, etc.). Focus on professional and property facts, not the
  person's demographics or neighborhood "character".
- **Multiple matches → ask.** If `fub_search_contacts` returns more than one person, or the name is
  common on the web, list the candidates and ask the agent which one before going deep.
- **Keep it non-creepy.** Conversation hooks should be things you'd happily say you found online
  (their recent business award, their team's listing). If a hook would feel weird to admit, drop it.

## Output format

Keep the dossier tight and scannable. Always label sources so the agent knows what's solid vs. what
to verify.

```markdown
# Contact Dossier — [Name]

## CRM Snapshot — *from your CRM*
- Stage: [e.g. New Lead / Nurture / Active Buyer]
- Source: [e.g. Zillow, referral, open house]
- Owner: [assigned agent]
- Key fields: [price range, area, timeline, tags — only what's populated]
- Notes worth knowing: [1–2 highlights from existing notes]

## Recent Activity — *from your CRM*
- [date] — [last touch: call / email / property view / form fill]
- [date] — [prior touch]
- Last contact: [N days ago] · Responsiveness: [e.g. opens emails, hasn't replied in 2 wks]

## Public Background — *from the web (verify)*
- [Fact] *(source, date)*
- Real-estate history: [prior sale/purchase if public] *(source, date)*
- Work: [employer/role if public] *(source, date)*
- Public social presence: [platforms/handles, general activity] *(source, date)*
- Neighborhood/market context: [area facts] *(source, date)*
- Unverified / unclear: [anything you couldn't confirm]

## Conversation Hooks
- [Genuine, specific talking point grounded in a fact above]
- [Another — tie to their timeline, niche, or a shared interest]

## Suggested Next Step
[One concrete action — e.g. "Call today; lead is hot (viewed 3 listings this week)."]
→ Drafting the outreach? Use **lead-reply**. Meeting booked? Use **appointment-prep**.
```

If the CRM isn't connected, drop the two *from your CRM* sections (or fill from what the agent tells
you) and note that connecting `~~crm` would add their internal history.

## Execution flow

1. **Load business context.** Call `get_my_profile` first — it returns the agent's business info and rules. Honor any relevant rules (e.g. follow-up timing, hours, service areas). If it's unavailable or returns "No profile configured," fall back to `CLAUDE.md` in the working folder.
2. **Identify the contact.** Take the name/email from the agent. If `~~crm` is connected, run
   `fub_search_contacts` to find the record. If it returns multiple matches, list them (name, stage,
   area) and ask which one — do not guess.
3. **Pull the CRM record.** With the contact ID, call `fub_get_contact` for the snapshot fields and
   `fub_get_contact_activity` for recent touches. Summarize stage, source, owner, timeline, and the
   last few interactions. These are *from your CRM* — stated as fact.
4. **Search the web for public background.** Use Claude web search for: public real-estate history,
   employer/role, public social presence, and neighborhood/market context. Prefer authoritative
   sources (county/MLS-adjacent public records, LinkedIn, the person's own pages). Capture a source
   and date for each fact.
5. **Verify and tag.** Cross-check anything that matters before stating it. Tag each web fact with
   its source and recency. Mark anything you couldn't confirm as *unverified*. Discard fabrications
   and any protected-class inferences (Fair Housing).
6. **Build conversation hooks.** Turn the strongest, most natural facts into 2–3 genuine talking
   points. Skip anything that would feel intrusive to mention out loud.
7. **Recommend a next step.** Based on stage + activity, suggest one concrete action, and point to
   `lead-reply` (to draft outreach) or `appointment-prep` (if a meeting is set).
8. **Deliver the dossier** in the format above, keeping the *from your CRM* and *from the web (verify)*
   halves clearly separated so the agent always knows what's confirmed internally vs. what to double-check.

## Tips

- Lead with the snapshot; agents often just need stage + last touch + one hook before dialing.
- When the web turns up little, say so plainly rather than padding — a short honest dossier beats a
  speculative long one.
- If the agent is about to call right now, front-load the single most useful hook and the next step.

## Related skills

- **lead-reply** — draft the actual message once you know who you're talking to.
- **appointment-prep** — deeper prep when a meeting or showing is booked.
- **daily-briefing** — surfaces which contacts are worth researching today.
