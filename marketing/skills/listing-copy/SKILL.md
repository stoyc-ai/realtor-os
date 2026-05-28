---
name: listing-copy
description: >
  Write a full property listing description from raw details — a headline, an MLS-style long
  description, and a short/social variant — all in the agent's own voice. Use when the agent says
  "write listing copy", "listing description for [address]", "describe this listing", "write the MLS
  description", or pastes property details and asks for copy. Loads the agent's brand voice first and
  enforces Fair Housing throughout.
metadata:
  version: "1.0.0"
---

# Listing Copy

Turn raw property details into ready-to-publish listing copy that sounds like *the agent* — not
generic AI — and is safe to post on the MLS and anywhere else. Produces three pieces from one input:
a **headline**, a **full MLS-style description**, and a **short/social variant**. This is a
**draft-only** content skill: it writes the copy and hands it back.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent pastes the property details (address, beds/baths, sqft, features, price)
  ✓ Loads the agent's voice so the copy sounds like them (sign-off, hashtag habits, tone)
  ✓ Output: headline + full MLS-style description + a short/social variant
SUPERCHARGED (when connected)
  + get_my_profile: pulls brokerage, service areas, niche, and saved voice automatically
  + Canva / Figma: hand the short variant off as caption text for a listing graphic
```

## Load first (every time)

1. **Load the agent's voice.** Call the `get_my_profile` tool first — it returns the agent's brand
   voice (tone, writing examples, sign-off, hashtags) + business info (brokerage, service areas,
   niche, rules). Use it as the voice/style source. If it's unavailable or returns "No profile
   configured," fall back to reading `voice-profile.md` and `CLAUDE.md` from the working folder; if
   neither exists, write in a warm, plain-spoken default and suggest running **learn-my-voice**.

- **`voice-profile.md`** (from **learn-my-voice**) — match tone, rhythm, and emoji/hashtag habits,
  and especially the few-shot examples.
- **`CLAUDE.md`** (from **remember**) — honor business facts and any "don't" rules, and use the
  sign-off from here on the social variant.

## Fair Housing guardrail (critical for listing copy)

Describe the **home, its features, and objective area facts** — never the **buyer** and never who
lives in the neighborhood.

- **Do:** square footage, layout, finishes, upgrades, lot, schools by name/rating *if publicly
  available as a fact*, walk score, distance to landmarks, HOA, year built.
- **Never:** reference or imply race, color, religion, national origin, sex, familial status, or
  disability. No steering or targeting language — drop phrases like "perfect for a young family,"
  "great starter home for newlyweds," "safe neighborhood," "ideal for empty nesters," "walking
  distance to church/temple," "exclusive community," "master bedroom" (use **primary bedroom**).
- **Describe the property, not the buyer.** "Single-level living with no stairs" describes the home;
  "great for seniors" targets a protected class — use the former.
- Never invent facts. If a detail (price, sqft, a feature) is missing, leave a clearly marked
  `[fill in: …]` placeholder rather than guessing.

## Output format

```markdown
# Listing Copy — [address]
**Specs:** [beds] bd · [baths] ba · [sqft] sqft · [price] · [type, if given]

---

## Headline
[One scroll-stopping line, ~6–12 words, in the agent's voice. No protected-class language.]

## MLS / Full description
[3–5 short paragraphs. Open with the strongest feature, walk the buyer through the home (flow,
finishes, primary suite, kitchen, outdoor space), then the location/area facts, then a clear close.
Plain text — no markdown asterisks — so it pastes clean into the MLS. ~120–200 words.]

## Short / social variant
[2–4 punchy lines for Instagram/Facebook captions, with 1–2 emoji only if that matches their voice.]

[Sign-off + hashtags from the profile, e.g. #JustListed #[Area]RealEstate]

---

**Want this as graphics?** I can hand the short variant to Canva/Figma as caption text, or run
**social-repurpose** to turn it into posts for each platform. Just say the word.
```

## Execution flow

1. **Parse the request** — get the address and details. If the agent only said "describe this
   listing," ask them to paste address, beds/baths, sqft, key features, and price.
2. **Load voice + business** — call `get_my_profile` first; fall back to `voice-profile.md` /
   `CLAUDE.md` if unavailable.
3. **Sort the details** — pick the 3–5 strongest selling features; note objective area facts; flag
   anything missing as `[fill in: …]`.
4. **Write the three pieces** — headline, full MLS description, short/social variant — all in their
   voice, applying the Fair Housing guardrail to every line.
5. **Self-check Fair Housing** — scan the copy for steering/targeting language and protected-class
   references; rewrite to describe the property, not the buyer.
6. **Output** using the template. Do not publish anywhere.
7. **Offer the next step** — graphics via Canva/Figma, or **social-repurpose** / **just-listed-sold**
   to extend the campaign.

## Related skills

- **social-repurpose** — spin this copy into Instagram/Facebook/LinkedIn posts + an IG Story.
- **just-listed-sold** — turn it into a just-listed / coming-soon / open-house announcement.
- **single-property-page** — build the SEO-optimized listing landing page.
- **learn-my-voice** — builds/updates the `voice-profile.md` this skill writes from.
