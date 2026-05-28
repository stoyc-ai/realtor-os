---
name: social-repurpose
description: >
  Turn ONE input — a listing, a blog post, or a topic — into native posts for Instagram, Facebook,
  LinkedIn, and an Instagram Story, each with a caption, hashtags, and a suggested visual idea. Use
  when the agent says "repurpose this", "turn this into social posts", "make social posts from this
  listing", or pastes content and asks to spread it across channels. Writes everything in the agent's
  voice and can hand the visual ideas to Canva.
metadata:
  version: "1.0.0"
---

# Social Repurpose

Take one piece of content and adapt it into platform-native posts — not the same caption pasted four
times. Each platform gets copy written for *how people actually use it*, in **the agent's voice**,
plus a concrete visual idea you can hand to Canva. Draft-only: it never posts anything.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent pastes ONE input (a listing's details, a blog/topic, or listing-copy output)
  ✓ Loads the agent's voice so every caption sounds like them
  ✓ Output: Instagram + Facebook + LinkedIn captions + an IG Story, each with hashtags + a visual idea
SUPERCHARGED (when connected)
  + get_my_profile: brand voice, brokerage, areas, niche, default hashtags
  + Canva / Figma: turn each "visual idea" into an actual graphic/Story frame
```

## Load first (every time)

1. **Load the agent's voice.** Call the `get_my_profile` tool first for brand voice (tone, examples,
   sign-off, hashtags) + business info. If unavailable or "No profile configured," fall back to
   `voice-profile.md` and `CLAUDE.md`; if neither exists, use a warm default and suggest
   **learn-my-voice**.

- **`voice-profile.md`** — match tone, rhythm, emoji and hashtag habits, and the few-shot examples.
- **`CLAUDE.md`** — honor business facts, default hashtag set, and any "don't" rules.

## Fair Housing guardrail

Describe the **home/topic and objective area facts** — never the buyer or who lives in an area. No
steering or targeting: drop "perfect for families," "safe/exclusive neighborhood," "great starter
home," "ideal for [life stage]." Use **primary bedroom**, not "master." Describe the property, not
the buyer. Don't invent facts; mark unknowns as `[fill in: …]`.

## Per-platform intent

- **Instagram** — visual-first; punchy hook in the first line, scannable, emoji if it fits their
  voice, a clear ask (DM / link in bio), 8–15 niche + local hashtags.
- **Facebook** — slightly longer, conversational, community tone; a question or ask that invites
  comments; 0–3 hashtags (FB rewards conversation, not tags).
- **LinkedIn** — professional, insight-led; lead with a takeaway or market angle, not a hard sell;
  no emoji-heavy style; 3–5 broad professional/local hashtags.
- **Instagram Story** — ultra-short on-frame text + a sticker/ask idea (poll, link, "DM ME"); built
  to be skimmed in 2 seconds.

## Output format

```markdown
# Social Repurpose — [source: address / topic]

---

## Instagram (feed)
[Caption in voice — hook first line, body, CTA.]
Hashtags: #[...] (8–15)
🎨 Visual idea: [specific — e.g. "carousel: 4 hero photos + price overlay on slide 1"]

## Facebook
[Caption in voice — conversational, a question/CTA to drive comments.]
Hashtags: #[...] (0–3)
🎨 Visual idea: [specific]

## LinkedIn
[Caption in voice — insight-led, professional.]
Hashtags: #[...] (3–5)
🎨 Visual idea: [specific — e.g. "clean single image, market stat overlay"]

## Instagram Story
On-screen text: [1–2 short lines]
Sticker/CTA: [poll / link / "DM ME" / countdown]
🎨 Visual idea: [vertical 9:16 — e.g. "listing photo + swipe-up to single-property page"]

[Sign-off from the profile, if they use one on social]
```

## Execution flow

1. **Parse the input** — identify the one source (listing details, blog, or topic). If nothing's
   pasted, ask for it.
2. **Load voice + business** — `get_my_profile` first; fall back to the saved files.
3. **Find the core message** — the single idea/hook to carry across all four formats.
4. **Adapt per platform** — rewrite for each channel's intent (above), in their voice, with the right
   hashtag count and a concrete visual idea for each.
5. **Self-check Fair Housing** — scan every caption for steering/targeting language; fix.
6. **Output** using the template.
7. **Offer the next step** — "Want me to build any of these in **Canva** (or **Figma**)?" If a design
   connector is present, offer to generate the graphic/Story from the visual idea; if not, leave the
   visual idea as a clear brief the agent can build manually.

## Related skills

- **listing-copy** — generate the source listing description this reuses.
- **just-listed-sold** — for announcement-style posts (just listed / sold / coming soon / open house).
- **reels-script** — turn the same input into a short-form video script.
- **content-calendar** — schedule the reused posts across the week.
- **learn-my-voice** — builds/updates the `voice-profile.md` this skill writes from.
