---
name: content-calendar
description: >
  Plan and build a real estate agent's social/content calendar — a dated schedule of what to post,
  where, and the hook — for a week or a month. Use when the agent says "build me a content calendar",
  "plan my content", "what should I post this month", "build my posting schedule", "give me a month of
  content", or "plan my social media". Uses their market + niche and seasonal/market moments, with
  every hook written in their voice.
metadata:
  version: "1.0.0"
---

# Content Calendar

Hand the agent a ready-to-go posting plan — dates, where to post, what kind of post, and an opening
line for each — built around *their* market, niche, and the time of year. It's a **planning** skill:
it produces the calendar (a table they can copy, save, and adjust). It doesn't publish anything; each
row is a quick brief that other skills (just-listed-sold, reels-script, market-update-post, etc.) can
turn into the actual post.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent says the timeframe (this week / this month) and optionally platforms + cadence
  ✓ Loads voice + market via get_my_profile so hooks sound like them and target their area/niche
  ✓ Output: a dated calendar TABLE (date, platform, content type, topic/hook), hooks in their voice
SUPERCHARGED (when connected)
  + get_my_profile: service areas, niche, brokerage, posting cadence/rules → tailors the mix
  + Google Calendar: read the agent's open houses / listing dates / events to anchor real posts
  + dashboards (Core): save the calendar as a live, iterable artifact instead of a one-off table
```

## Load first (every time)

1. **Load voice + business.** Call `get_my_profile` first — it returns brand voice and business facts
   (service areas, target neighborhoods, niche, brokerage, any cadence rules like *"post M/W/F"* or
   *"no posting on Sundays"*). Use voice for hooks and the market facts to make topics local and
   specific. Fallback: `voice-profile.md` + `CLAUDE.md` in the working folder; if neither, ask for
   their area, niche, and preferred platforms.

## Fair Housing guardrail

Topics and hooks describe **homes, the market, the area's amenities, and the process** — never target
or imply preference by protected class (no "perfect post for young families", "great area for
[group]"). When a topic touches neighborhoods, keep it to objective facts (price trends, inventory,
amenities, walkability) — not who lives there or who "should" live there.

## Build the plan

```
1. Timeframe + cadence
   - Week → ~3–5 slots. Month → a realistic cadence (e.g. 3–4 posts/week), not daily unless asked.
   - Honor any cadence/no-post rules from the profile.
2. Platforms → Instagram, Facebook, TikTok/Reels, LinkedIn, YouTube, GBP — use the ones they actually
   use; default to IG + FB if unspecified, and ask only if it matters.
3. Balance the content mix (don't make it all listings):
   - Listings & sales (just listed / open house / under contract / sold / coming soon)
   - Educational (buyer/seller tips, process, financing basics, mistakes to avoid)
   - Market updates (local stats, rates, inventory — for that area)
   - Local / community (neighborhood spotlights, local businesses, events)
   - Personal / brand (behind-the-scenes, testimonials, agent story, FAQ)
   - Engagement (polls, "this or that", Q&A prompts)
4. Anchor to real moments → seasonality, holidays, and market timing for the agent's area:
   - Seasonal: spring selling season, back-to-school, fall, year-end "what's my home worth"
   - Holidays/observances relevant to real estate marketing
   - Market moments: rate changes, new listings going live, open-house weekends (pull from Calendar
     if connected; otherwise leave a [your open house?] prompt)
5. Write one hook per slot in the agent's voice — the scroll-stopping first line, not a topic label.
```

Never invent specific listing facts or events. If a slot depends on the agent's input (a real open
house, a new listing), mark it `[fill in: …]` so the calendar stays honest.

## Output format

```markdown
# Content Calendar — [Week of / Month of …] · [Platforms]
**Voice:** [source] · **Market:** [areas/niche] · **Cadence:** [e.g. 4×/week, M–F]

| Date | Platform | Content type | Topic / Hook (in your voice) |
|------|----------|--------------|------------------------------|
| [Mon, Jun 2] | Instagram | Just Listed | "[hook]" — [1-line note: photos/reel] |
| [Tue, Jun 3] | Facebook | Market update | "[hook]" — [stat to pull] |
| [Wed, Jun 4] | Reels/TikTok | Buyer tip | "[hook]" — [reels-script can write this] |
| … | … | … | … |

**Themes this period:** [seasonal/market angle driving the plan]
**To turn a row into a post:** just-listed-sold · reels-script · market-update-post · newsletter.

> Want this as a **living artifact** you can check off and update? I can build it with the
> **dashboards** skill so it stays in your workspace and updates as you go.
```

Keep hooks specific and in-voice ("3 things that kill a Willow Glen sale in week one") — not generic
labels ("post a buyer tip").

## Execution flow

1. **Parse the request** — timeframe (week/month), platforms, any cadence.
2. **Load voice + market** — `get_my_profile` first; fall back to `voice-profile.md` / `CLAUDE.md`.
3. **Pull real anchors** (connected) — read Google Calendar for open houses/events/listing dates to
   seed real posts; otherwise leave prompts for the agent's events.
4. **Decide the mix + cadence** — balance listing, educational, market, local, personal, engagement;
   respect no-post rules.
5. **Map to seasonality/holidays/market moments** for their area and timeframe.
6. **Write one in-voice hook per slot**, applying the Fair Housing guardrail.
7. **Render the calendar table** using the template; note which skill turns each row into a post.
8. **Offer to save/iterate** — mention it can be edited, and point to **dashboards** if they want it
   as a live artifact.

## Write safety

- **Planning only** — this skill produces a calendar; it doesn't publish or schedule posts.
- Don't fabricate listings, events, or stats — placeholder anything that needs the agent's real data.
- The optional save-as-artifact step happens only if the agent asks for it (hands off to dashboards).

## Related skills

- **just-listed-sold** / **reels-script** / **market-update-post** / **newsletter** — turn calendar
  rows into finished posts.
- **dashboards** (Core) — save the calendar as a live, iterable artifact in the workspace.
- **learn-my-voice** — refines the `voice-profile.md` the hooks are written from.
