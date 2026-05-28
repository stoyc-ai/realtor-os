---
name: listing-launch-kit
description: >
  One command turns a new listing into a complete marketing rollout — listing description, social
  posts for every platform, a "just listed" email, a landing-page draft, and a short-form video
  script — all in the agent's voice and ready to use. Use when the agent says "launch this listing",
  "listing launch kit for [address]", "create all the marketing for [listing]", "new listing — make
  everything", or "market my new listing". Produces the whole kit in one pass.
metadata:
  version: "1.0.0"
---

# Listing Launch Kit

Drop in a new listing and get back everything you need to market it — in one organized kit, written
in *your* voice. Instead of running five skills one at a time, this builds the full rollout in a
single pass and labels each piece so you can copy/paste it or hand it to the right tool. Draft-only:
it writes the whole kit and hands it back; it never posts or sends anything.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent pastes the property details (address, price, beds/baths, sqft, standout features, photos if any)
  ✓ Loads the agent's voice so every piece sounds like them
  ✓ Output: one organized kit — listing description + social posts + email + landing page + video script
SUPERCHARGED (when connected)
  + get_my_profile: brand voice, brokerage, service areas, niche, default hashtags
  + Canva / Figma: turn each "visual idea" into an actual graphic or Story frame
  + Mailchimp / Klaviyo: load the announcement email as a campaign draft
  + Webflow / WordPress: publish the single-property page draft
```

## Load first (every time)

1. **Load the agent's voice.** Call the `get_my_profile` tool first — it returns the agent's brand
   voice (tone, writing examples, sign-off, hashtags) + business info (brokerage, service areas,
   niche, rules). Use it as the voice/style source for every asset. If it's unavailable or returns
   "No profile configured," fall back to reading `voice-profile.md` and `CLAUDE.md` from the working
   folder; if neither exists, write in a warm, plain-spoken default and suggest **learn-my-voice**.

- **`voice-profile.md`** (from **learn-my-voice**) — match tone, rhythm, emoji/hashtag habits, and
  especially the few-shot examples.
- **`CLAUDE.md`** (from **remember**) — honor business facts, default hashtag set, sign-off, and any
  "don't" rules.

## Fair Housing guardrail (critical — applies to every asset)

Describe the **home, its features, and objective area facts** — never the **buyer** and never who
lives in the neighborhood.

- **Do:** square footage, layout, finishes, upgrades, lot, year built, HOA, schools by name/rating
  *if publicly available as a fact*, walk score, distance to landmarks.
- **Never:** reference or imply race, color, religion, national origin, sex, familial status, or
  disability. No steering or targeting — drop "perfect for a young family," "great starter home,"
  "safe neighborhood," "ideal for empty nesters," "exclusive community," "walking distance to
  church/temple." Use **primary bedroom**, not "master."
- **Describe the property, not the buyer.** "Single-level living, no stairs" describes the home;
  "great for seniors" targets a protected class — use the former.
- Never invent facts. Mark anything missing as `[fill in: …]` rather than guessing.

## What the kit includes

| Asset | What it is | Hand to |
|-------|-----------|---------|
| (a) Listing description | Full MLS-style description + a short version | MLS / paste anywhere |
| (b) Social posts | Instagram, Facebook, LinkedIn + an IG Story (each: caption, hashtags, visual idea) | Canva / each platform |
| (c) "Just listed" email | Announcement email with subject lines | Mailchimp / Klaviyo |
| (d) Landing page | Single-property page draft | Webflow / WordPress |
| (e) Video script | Short-form Reel/TikTok script | film / Canva |
| (f) "Coming soon" teaser | *Optional* pre-launch teaser post | social, if going live later |

## Output format

```markdown
# Listing Launch Kit — [address]
**Specs:** [beds] bd · [baths] ba · [sqft] sqft · [price] · [type, if given]
**Standout features:** [3–5 picked]

---

## (a) Listing description

### Full (MLS-style)
[3–5 short paragraphs, plain text so it pastes clean into the MLS. ~120–200 words.]

### Short version
[2–4 punchy lines for captions/quick use.]

---

## (b) Social posts

### Instagram (feed)
[Caption in voice — hook first line, body, CTA.]
Hashtags: #[...] (8–15)
🎨 Visual idea: [specific — e.g. "carousel: 4 hero photos + price overlay on slide 1"]

### Facebook
[Caption in voice — conversational, a question/CTA to drive comments.]
Hashtags: #[...] (0–3)
🎨 Visual idea: [specific]

### LinkedIn
[Caption in voice — insight-led, professional.]
Hashtags: #[...] (3–5)
🎨 Visual idea: [specific]

### Instagram Story
On-screen text: [1–2 short lines]
Sticker/CTA: [poll / link / "DM ME" / countdown]
🎨 Visual idea: [vertical 9:16 — e.g. "listing photo + link to the landing page"]

---

## (c) "Just listed" announcement email
**Subject A:** [option] **Subject B:** [option]
[Email body in voice — short, scannable, one clear CTA to book a showing / view the page.]
[Sign-off from the profile]

---

## (d) Single-property landing page (draft)
**Page title / H1:** [headline]
**Meta description:** [~150 chars]
**Sections:** Hero ([address + price]) · Highlights ([bullets]) · About the home ([2–3 paras]) ·
Neighborhood facts ([objective]) · Gallery ([photo slots]) · Contact / book a showing ([CTA + agent info])

---

## (e) Short-form video script (Reel / TikTok)
**Hook (0–3s):** [scroll-stopping line]
**Shots:** [3–6 quick beats — what's on screen + on-screen text]
**Voiceover/caption:** [in voice]
**CTA:** [DM / link / "comment ADDRESS"]
🎵 [trending-audio idea, optional]

---

## (f) Coming-soon teaser  *(optional — include if going live later)*
[Short teaser caption + visual idea.]

---

### Where to post each
- **(a)** → MLS + your website  ·  **(b)** → Instagram, Facebook, LinkedIn, IG Story
- **(c)** → Mailchimp/Klaviyo to your list  ·  **(d)** → Webflow/WordPress  ·  **(e)** → Reels/TikTok
```

## Execution flow

1. **Parse the request** — get the address + details. If they only said "launch this listing," ask
   them to paste address, price, beds/baths, sqft, standout features, and photos if any.
2. **Load voice + business** — call `get_my_profile` first; fall back to `voice-profile.md` /
   `CLAUDE.md` if unavailable.
3. **Sort the details** — pick the 3–5 strongest selling features; note objective area facts; flag
   anything missing as `[fill in: …]`. Decide whether to include the optional "coming soon" teaser
   (only if they're launching later).
4. **Build every asset in one pass** — (a) description, (b) social posts, (c) email, (d) landing
   page, (e) video script, (f) optional teaser — all in their voice, reusing the core hook across
   pieces so the rollout feels consistent.
5. **Self-check Fair Housing** — scan every asset for steering/targeting language and protected-class
   references; rewrite to describe the property, not the buyer.
6. **Output** the full kit using the template, clearly labeled, with the "where to post each" line.
   Do not publish or send anything.
7. **Offer the next step** — "Want me to build the graphics in **Canva**, load the email into
   **Mailchimp**, or publish the page in **Webflow**?" If a connector is present, offer to push that
   asset; if not, leave it as a clean draft to paste in manually.

## Related skills

- **listing-copy** — generate just the listing description on its own.
- **social-repurpose** — spin one piece into platform-native posts.
- **just-listed-sold** — standalone just-listed / coming-soon / open-house / sold announcements.
- **reels-script** — a deeper short-form video script on its own.
- **single-property-page** — build and publish the full listing landing page.
- **newsletter** — fold the new listing into the agent's regular email newsletter.
- **learn-my-voice** — builds/updates the `voice-profile.md` this kit writes from.
