---
name: sphere-radar
description: >
  Proactive relationship radar that scans the agent's sphere for the life and money moments that come
  right before a move — so they can reach out first. Use when the agent says "sphere radar", "liquidity
  radar", "relationship radar", "check my sphere", "who in my sphere had news", "any updates on my
  contacts", "who should I reach out to", or sets it up as a weekly scheduled task. Reads a list of
  people (a Google Sheet or the CRM), verifies recent news/LinkedIn/press for major events, and hands
  back a prioritized table with a warm, no-ask congratulations note drafted in the agent's voice.
metadata:
  version: "1.0.0"
---

# Sphere Radar

The agent's next deal is usually already in their phone — they just don't know who's about to move.
Sphere Radar watches the people they know for the moments that precede a real-estate decision (a
company sale, a new role in another city, a promotion, a new baby, an award) and surfaces them while
they're still fresh, with a warm note ready to send. The goal is to be the agent who reaches out
*first*, with no ask — so when a move does happen, they're the obvious call.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent pastes/links a list of people (names + companies) → verify recent news → draft a note each
  ✓ Output: a prioritized table — who had news, why it matters to them, a ready note, and the source
SUPERCHARGED (when connected)
  + Google Drive/Sheets: read the agent's sphere straight from a named Google Sheet
  + ~~crm (Follow Up Boss): pull the sphere (past clients, SOI, referral partners) automatically
  + Gmail: drop the drafted notes straight into the agent's drafts to send
```

Verified web search powers the radar itself — no special connector needed to find the news.

## Before you start

Load voice + rules, then settle the inputs:

- **Call `get_my_profile`** (on `~~crm`) first for the agent's voice (tone, sign-off) and business
  facts + rules. Fall back to `voice-profile.md` (voice) and `CLAUDE.md` (rules) in the working
  folder; if voice is missing, write warmly and plain and suggest **learn-my-voice**.
- **Find the people.** In order: (1) a **Google Sheet** the agent names — read names + companies (and
  any title/city/email columns); (2) the **CRM** sphere via `fub_list_leads` / `fub_search_contacts`
  (past clients, SOI tags, referral partners); (3) if neither is available, ask the agent to paste a
  list. Confirm which source before running.
- **Set the window.** Default to the **past 7 days**. Honor a different window if the agent asks
  (e.g. "past month" for a first run on a fresh list).
- **Read the dedupe log.** Check `sphere-radar-log.md` in the working folder for events already
  surfaced in past runs; never re-flag the same event. (Create the log if it doesn't exist.)

## What counts as a hit — trigger taxonomy (scored by why it matters)

Don't just look for "professional events." Look for the moments that move real estate, and tag each
hit with **why it matters to the agent** so they know how to act:

| Category | Signals to catch | Why it matters |
|----------|------------------|----------------|
| 💰 **Liquidity** | Company sale or acquisition, IPO, funding round, big exit, stock vesting, lottery/inheritance press | New buying power — move-up, second home, or investment |
| 🧭 **Relocation** | New role in another city, company HQ move, return-to-office mandate, expansion | A move is likely on the table |
| 📈 **Career** | Promotion, new C-suite/exec role, board seat, partnership, retirement | Upgrade, downsize, or more disposable income |
| 🏡 **Life** | Engagement/marriage, new baby, kids heading to college, empty nest | Space needs change → buy or sell |
| 🏆 **Touch-point** | Award, notable press, speaking gig, business milestone, anniversary | A warm, no-reason-needed reason to reconnect |

Combine signals — a promotion *plus* a relocation outranks a standalone award. When the signal is
thin or ambiguous, say so instead of inventing urgency.

## Sources to check

Lead with the two the agent named (recent news + public LinkedIn), then widen where it adds value:

- **News & press** — recent articles, press releases, the company's newsroom.
- **Public LinkedIn** — role changes, "starting a new position," posts (public only).
- **Local business journals** (e.g. the city's Business Journal) — promotions, expansions, deals.
- **Funding/IPO sources** — Crunchbase-style signals, SEC/IPO news for liquidity events.
- **Company website / Google Business** — leadership changes, new locations.
- **The agent's own connectors** when present (Gmail/Calendar can hint at recent interactions worth a touch).

Use judgment on which extra sources fit each person — and always keep a source link.

## Verification rules (don't embarrass the agent)

- **Source-backed only.** Every hit needs a real, linkable source dated **inside the window**. No
  source → it doesn't go on the list.
- **Right person.** Disambiguate common names — match on company, role, city, or photo/profile before
  attributing news. If you can't confirm it's *their* contact, skip it and note why.
- **Fresh, not recycled.** Skip events already in `sphere-radar-log.md`, and skip old news
  resurfacing in a new article unless the event itself is within the window.
- **No fabrication.** Never infer or embellish. "No verified updates this week" is a perfectly good
  result — say it plainly.

## The note — warm, specific, no ask

For each verified hit, draft a short congratulations **in the agent's voice**:

- **Specific** — reference the actual event ("saw the news about the Series B — huge").
- **No ask, no pitch.** Pure relationship. No "and if you ever need a realtor…". The restraint is the
  strategy; the referral comes later because the touch felt genuine.
- **Right channel** — suggest the natural one per person: a LinkedIn comment/DM for a LinkedIn post, a
  text for a close past client, an email for a referral partner. Note it in the table.
- **Short.** One to three sentences. It should read like the agent tapped it out themselves.

## Prioritization

Rank the table **most-worth-it first**: relevance (Liquidity/Relocation > Touch-point) × recency ×
relationship strength (past client/referral partner > cold sphere name). Call out the single top
mover above the table so a busy agent acts on it even if they read nothing else.

## Output format

```markdown
# Sphere Radar | Week of [Month Date] · [N] verified updates
🔥 **Act first:** [Name] — [what happened] ([category: why it matters])

| Person | What happened | Why it matters | Channel | Draft note | Source |
|--------|---------------|----------------|---------|------------|--------|
| [Name] · [company] | [one line] | [💰/🧭/📈/🏡/🏆 + a few words] | [LinkedIn/Text/Email] | "[note in their voice]" | [link] |

_Checked [N] people · [N] had verified news · [N] skipped (no updates) · window: past [7] days._

*Want me to drop these into your Gmail drafts, or set this to run automatically every week? Just say so.*
```

If nothing verifies: say *"No verified updates across your [N] contacts this week — I'll keep watching."*

## Run it weekly on autopilot

This skill is built to run hands-off. To schedule it (Cowork → Scheduled → New task, weekly), use:

> "Every Monday at 8am, run Sphere Radar on my Google Sheet called **[Sheet name]** (or my CRM
> sphere). Check the past week for verified news, draft a warm no-ask note for each hit in my voice,
> and give me the prioritized table. Skip anything you already flagged before."

Pick a day/time the laptop is on; scheduled tasks run while the app is open. The dedupe log keeps each
week's list fresh, not repetitive.

## Fair Housing & privacy guardrail

- **Public info only.** Use publicly available news and profiles. Don't dig for or store private
  details; keep notes about the *professional/life milestone*, never sensitive personal data.
- **Fair Housing.** Never attribute, rank, or word anything around a protected class (race, religion,
  familial status, national origin, disability, sex). "New baby → space change" frames the *housing
  need*, never the person. Describe the milestone and the move, not who they are.
- **Always agent-approved.** Every note is a draft; the agent reads and sends. Nothing goes out
  automatically.

## Related skills

- **learn-my-voice** — so every congratulations note actually sounds like the agent.
- **follow-up** — once someone replies, set the next touch and keep the thread warm.
- **lead-reply** — if a sphere touch turns into a real buying/selling conversation.
- **remember** — save the agent's sphere sheet name or preferred outreach rules so it's automatic next time.
