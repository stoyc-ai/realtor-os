---
name: just-listed-sold
description: >
  Write real estate announcement posts — just-listed, just-sold, coming-soon, and open-house — with
  per-platform copy and hashtags, all in the agent's voice. Use when the agent says "just listed
  post", "just sold post for [address]", "coming soon post", "open house post", or asks to announce a
  listing milestone. Loads the agent's brand voice first and enforces Fair Housing.
metadata:
  version: "1.0.0"
---

# Just Listed / Sold

Produce the four core announcement posts agents run all year: **just-listed**, **just-sold**,
**coming-soon**, and **open-house**. Each comes back as per-platform copy with hashtags, in **the
agent's voice**, ready to paste (and a visual idea to hand to Canva). Draft-only — never posts.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent says which announcement + pastes the details (address, specs, price/terms, open-house time)
  ✓ Loads the agent's voice so the post sounds like them
  ✓ Output: Instagram + Facebook + LinkedIn announcement copy + hashtags + a visual idea
SUPERCHARGED (when connected)
  + get_my_profile: brand voice, brokerage, service areas, default hashtags
  + Canva / Figma: turn the announcement into a branded graphic / Story
```

## Load first (every time)

1. **Load the agent's voice.** Call `get_my_profile` first for brand voice + business info. If
   unavailable or "No profile configured," fall back to `voice-profile.md` and `CLAUDE.md`; if
   neither exists, use a warm default and suggest **learn-my-voice**.

- **`voice-profile.md`** — tone, rhythm, emoji/hashtag habits, few-shot examples.
- **`CLAUDE.md`** — brokerage, sign-off, default hashtags, and any "don't" rules.

## Fair Housing guardrail

Announce the **home and the facts** — never the buyer/seller or the neighborhood's makeup. No
celebrating *who* bought ("sold to a lovely young family"), no steering ("perfect family
neighborhood," "safe area," "exclusive community"). Use **primary bedroom**, not "master." Describe
the property and the milestone, not the people. Don't invent facts — mark unknowns as `[fill in: …]`.

## Announcement types (what each needs)

- **Coming soon** — build anticipation; tease the best feature, *don't* over-share; "hitting the
  market soon," a "DM for first look" ask. Note any MLS coming-soon rules belong to the agent.
- **Just listed** — celebrate it's live; lead with the hook, key specs, price, and a clear ask (book
  a showing / link in bio).
- **Open house** — date, time, address; what to expect; "swing by" ask; create urgency.
- **Just sold** — gratitude + social proof (not buyer details); "another one closed," a soft "thinking
  of selling?" ask. For listings the agent *sold*, keep it about the result, not the people.

## Output format

```markdown
# [Just Listed / Just Sold / Coming Soon / Open House] — [address]
**Details:** [specs · price/terms · open-house date+time, as applicable]

---

## Instagram
[Caption in voice — hook, key details, CTA.]
Hashtags: #JustListed #[Area]RealEstate #[...] (8–15)
🎨 Visual idea: [specific — e.g. "hero photo + 'JUST LISTED' banner + price"]

## Facebook
[Caption in voice — conversational, CTA that invites comments/shares.]
Hashtags: #[...] (0–3)

## LinkedIn
[Caption in voice — professional, brief; frame as a market/business note.]
Hashtags: #[...] (3–5)

[Sign-off from the profile]
```

(For **open house**, lead every caption with the date/time/address. For **coming soon**, keep
specifics light and push the "first look" ask.)

## Execution flow

1. **Parse the request** — which announcement type + the property details and any timing. If missing,
   ask for address, specs, price/terms, and (for open house) date + time.
2. **Load voice + business** — `get_my_profile` first; fall back to the saved files.
3. **Pick the angle** for the chosen type (above) and pull the strongest hook.
4. **Write per-platform copy** — Instagram, Facebook, LinkedIn — in their voice, with correct hashtag
   counts and a visual idea.
5. **Self-check Fair Housing** — scan for any reference to buyers/sellers or neighborhood makeup, and
   any steering/targeting language; fix.
6. **Output** using the template.
7. **Offer the next step** — build the graphic in **Canva**/**Figma** if connected (else leave the
   visual idea as a brief); or run **social-repurpose** for an IG Story version.

## Related skills

- **listing-copy** — write the full property description these announcements summarize.
- **social-repurpose** — expand into more channels + an IG Story.
- **open-house-capture** (Core) — capture and follow up on open-house visitors.
- **content-calendar** — schedule the announcement sequence.
- **learn-my-voice** — builds/updates the `voice-profile.md` this skill writes from.
