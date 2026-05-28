---
name: market-update-post
description: >
  Write a market-update social post for the agent's area — a punchy caption plus 2–4 current, cited
  local stats — in the agent's voice. Use when the agent says "market update post", "market stats
  post", "monthly market update", or asks to share local market numbers. Loads the agent's area from
  their profile, researches current stats via web search, and cites every number.
metadata:
  version: "1.0.0"
---

# Market Update Post

Give the agent a ready-to-post market update for *their* market: a scroll-stopping caption plus a
few **real, sourced** stats. Numbers come from web search and are always cited with a source and
date — this skill **never fabricates figures**. Written in **the agent's voice**, draft-only.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent gives (or profile provides) their service area / market
  ✓ Web search pulls current local stats; caption written in their voice
  ✓ Output: punchy caption + 2–4 cited stats + hashtags + a visual idea
SUPERCHARGED (when connected)
  + get_my_profile: pulls their service areas, niche, brokerage, and voice automatically
  + Canva / Figma: turn the stats into a branded stat-card graphic
```

## Load first (every time)

1. **Load the agent's voice + area.** Call `get_my_profile` first — it returns brand voice + business
   info, including **service areas/target neighborhoods**. Use those to scope the stats. If
   unavailable or "No profile configured," fall back to `voice-profile.md` and `CLAUDE.md`; if the
   area still isn't known, **ask** which market/zip/neighborhood to cover. Never assume an area.

- **`voice-profile.md`** — tone, rhythm, emoji/hashtag habits, few-shot examples.
- **`CLAUDE.md`** — service area, sign-off, default hashtags, "don't" rules.

## Research the stats (web search — cite everything)

Use Claude's built-in web search to find **current** figures for the agent's specific area. Aim for
2–4 of these, whichever are available and recent:

- Median sale / list price (and YoY or MoM change)
- Days on market (DOM)
- Active inventory / months of supply
- Sale-to-list price ratio
- Number of closed sales / new listings
- 30-yr mortgage rate (national, as context)

Rules:
- **Cite source + date for every stat** (e.g. "Redfin, April 2026" / "local MLS, Q1 2026"). Prefer
  authoritative sources: local MLS/Realtor association, Redfin, Realtor.com, Zillow, FHFA, FRED.
- **Never invent or estimate a number.** If a figure isn't findable, drop that stat rather than
  guessing, and note "couldn't verify [metric] — left out."
- Note the **as-of date**; markets move, so the post is a snapshot.

## Fair Housing guardrail

Report **market facts**, not who lives in or should move to an area. No steering ("great area for
families," "up-and-coming/safe neighborhood as a buying signal"). Keep it to objective metrics and
their implication for buyers/sellers generally. Describe the market, not the people.

## Output format

```markdown
# Market Update — [area] · [as-of date]

---

## Caption
[Punchy, in the agent's voice. Open with a hook ("Is it still a seller's market in [area]? Here's
what the numbers actually say…"), weave in the takeaway (what it means for buyers/sellers), close
with a clear ask (DM for a free home valuation / let's talk). Plain, scannable.]

Hashtags: #[Area]RealEstate #MarketUpdate #[...] (8–15 for IG; trim for other platforms)

🎨 Visual idea: [stat-card layout — e.g. "3 big numbers with up/down arrows, area name, your headshot"]

---

## Stats used (with sources)
1. **[Metric]:** [value] ([change]) — *[source, date]*
2. **[Metric]:** [value] ([change]) — *[source, date]*
3. …

> Snapshot as of [date]. Verify before posting; markets change.

[Sign-off from the profile]
```

## Execution flow

1. **Determine the area** — from `get_my_profile`; if absent, ask. Confirm the geographic scope
   (neighborhood vs. city vs. metro).
2. **Load voice** — `get_my_profile` first; fall back to the saved files.
3. **Research** — web search for 2–4 current stats for that exact area; record value, change, source,
   and date for each. Drop anything unverifiable.
4. **Write the caption** in their voice — hook → takeaway → clear ask — weaving in 2–3 of the stats.
5. **Self-check** — every number has a source + date; no fabricated figures; Fair Housing clean
   (facts, not steering).
6. **Output** using the template, including the sourced stat list.
7. **Offer the next step** — build a stat-card in **Canva**/**Figma** if connected (else leave the
   visual idea); or run **social-repurpose** to adapt across platforms.

## Related skills

- **social-repurpose** — adapt the update into per-platform posts + an IG Story.
- **reels-script** — turn the stats into a short-form "market in 30 seconds" video.
- **newsletter** — fold the same numbers into the monthly email.
- **seo-audit / blog-seo** — the long-form, search-friendly version of market content.
- **learn-my-voice** — builds/updates the `voice-profile.md` this skill writes from.
