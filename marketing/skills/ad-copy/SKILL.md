---
name: ad-copy
description: >
  Write Google and Meta (Facebook/Instagram) ad copy in the agent's own voice — headlines, primary
  text, and descriptions, with 2–3 variants to test. Use when the agent says "write ad copy", "write
  a facebook ad", "google ad for [campaign]", "instagram ad", "ad for my open house", or "ad to
  promote [listing/service]". Writing only — it produces copy to paste into Ads Manager / Google Ads;
  it does not connect to or touch any ad account.
metadata:
  version: "1.0.0"
---

# Ad Copy

Turn an ad goal into ready-to-use ad copy that sounds like *the agent* and fits where it's going —
Google search ads and Facebook/Instagram ads read very differently. This is a **writing-only** skill:
it hands back copy for the agent to paste into Google Ads or Meta Ads Manager. It is **not connected
to any ad account** and never launches, edits, or spends money on an ad.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent says the platform + what they're promoting (listing, open house, free guide, their service)
  ✓ Loads voice + business via get_my_profile so the copy sounds like them and names their market
  ✓ Output: platform-correct headlines + primary text + descriptions, 2–3 variants, in their voice
SUPERCHARGED (when connected)
  + get_my_profile: brokerage, service areas, niche, license #, sign-off — baked into the copy
  + (no ad connector by design — this skill writes; the agent places the ad)
```

## Load first (every time)

1. **Load the agent's voice + business.** Call `get_my_profile` first — it returns brand voice (tone,
   examples, hashtags) and business facts (brokerage, service areas, niche, license/DRE #). Use it as
   the voice/style source. If it's unavailable or returns "No profile configured," fall back to
   reading `voice-profile.md` and `CLAUDE.md` from the working folder; if neither exists, draft in a
   warm, plain-spoken default and suggest running **learn-my-voice**.

## Fair Housing + Special Ad Category (read this)

Housing-related ads run on Meta as a **Special Ad Category** and are bound by **Fair Housing** law.
This shapes the copy itself:

- **Never target or imply preference by protected class** — race, color, religion, national origin,
  sex, familial status (kids/no kids), or disability. No "perfect for young families", "great for
  retirees", "ideal bachelor pad", "safe Christian neighborhood", "no kids".
- **Sell the home and the offer, not the audience.** Describe features, price, location, and the
  action you want ("Tour this 3-bed in Willow Glen Saturday"), never *who* should respond.
- **No steering by area demographics** ("good schools for families like yours", characterizing a
  neighborhood by who lives there).
- **Remind the agent** in the output: when they place a housing ad on Meta, they must declare the
  **Special Ad Category: Housing**, which limits audience targeting (no age/gender/ZIP narrowing) —
  that's a setting in Ads Manager, not something this copy controls.

## Get the brief

Confirm (ask only what's missing — don't over-interrogate):

```
1. Platform → Google Search, Meta (Facebook/Instagram feed), or both.
2. Objective → leads, open-house attendance, listing views, brand/awareness, a downloadable guide.
3. The subject → which listing/service/event, key facts (price, beds/baths, address or area, date).
4. The offer + CTA → "Book a tour", "Get the free buyer guide", "See open house times", "Call/DM".
5. Landing destination → where the click goes (so the copy matches the page promise).
```

Never invent property facts (price, beds, dates). Use a clearly marked `[fill in: …]` placeholder for
anything unknown rather than guessing.

## Platform formats (write to these)

**Google Search (Responsive Search Ad):**
- **Headlines:** up to ~30 characters each — write several (8–10 ideal); Google rotates them.
- **Descriptions:** up to ~90 characters each — write 2–4.
- Front-load the keyword + location; include the CTA; no clickbait or all-caps gimmicks.

**Meta (Facebook / Instagram feed):**
- **Primary text:** ~125 characters before "See more" — lead with the hook.
- **Headline:** ~40 characters — the bold line under the image.
- **Description / link description:** ~30 characters — optional supporting line.
- Conversational, scroll-stopping first line; emoji only if it matches the agent's voice.

## Output format

```markdown
# Ad Copy — [Subject] · [Platform(s)] · [Objective]
**Voice:** [from profile / voice-profile.md / default] · **Market:** [areas from profile]

> Housing ad → on Meta, set **Special Ad Category: Housing**. Copy is Fair Housing–compliant:
> it sells the home/offer, never targets or implies a protected class.

---

## Google Search — Responsive Search Ad   ← (only if Google requested)
**Headlines** (≤30 chars each)
1. [headline]   2. [headline]   3. [headline]   4. [headline]   …
**Descriptions** (≤90 chars each)
1. [description]   2. [description]
**Display path:** /[path1]/[path2]   ·   **Final URL:** [landing destination]

## Meta — Facebook / Instagram   ← (only if Meta requested)

### Variant A — [angle, e.g. "Open house urgency"]
**Primary text:** [hook-first, ~125 chars before the fold]
**Headline:** [≤40 chars]   ·   **Description:** [≤30 chars]   ·   **CTA button:** [Learn More / Sign Up / Book Now]

### Variant B — [angle, e.g. "Lifestyle / feature-led"]
[same fields]

### Variant C — [angle, e.g. "Value / free guide"]   ← optional
[same fields]

---

**Notes:** [what to A/B test, any [fill in: …] placeholders, image/creative suggestion in one line]
```

Always give **2–3 distinct variants** with different angles (urgency, feature-led, value/lead-magnet)
so the agent can test — not three rewordings of the same line.

## Execution flow

1. **Parse the request** — platform(s), what's being promoted. Ask only for missing brief items.
2. **Load voice + business** — `get_my_profile` first; fall back to `voice-profile.md` / `CLAUDE.md`.
3. **Confirm the brief** — objective, subject + facts, offer/CTA, landing destination. Placeholder any
   unknown facts.
4. **Write to the platform format** — Google RSA headlines/descriptions and/or Meta primary
   text/headline/description, in the agent's voice, naming their real market.
5. **Produce 2–3 variants** with genuinely different angles.
6. **Apply Fair Housing + Special Ad Category** throughout, and surface the Special Ad Category
   reminder in the output.
7. **Output the copy** using the template. Do not connect to any ad account — this skill only writes.

## Write safety

- **Writing only.** No ad-account connector; this skill never launches, edits, pauses, or spends.
- All copy must pass the Fair Housing / Special Ad Category check before it's shown.
- Don't fabricate property facts, prices, or results ("sold in 1 day", "guaranteed") — use
  placeholders and avoid unverifiable claims.

## Related skills

- **content-calendar** — slot these ads/promotions into the posting plan.
- **just-listed-sold** — organic social posts for the same listing the ad promotes.
- **single-property-page** — build the landing page the ad clicks through to.
- **learn-my-voice** — refines the `voice-profile.md` this skill writes from if the copy "isn't me".
