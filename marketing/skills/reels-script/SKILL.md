---
name: reels-script
description: >
  Write a short-form video script for Reels, TikTok, or YouTube Shorts — a hook, a scene-by-scene
  shot list with on-screen text, and a suggested caption — in the agent's voice. Use when the agent
  says "reel script", "video script", "tiktok script for [topic]", "shorts script", or asks for a
  short video about a listing or topic. Loads the agent's brand voice first and enforces Fair Housing.
metadata:
  version: "1.0.0"
---

# Reels Script

Hand the agent a film-ready short-form script: a strong **hook**, a **scene-by-scene** breakdown
(what to say + what to show + on-screen text), and a **suggested caption** — all in **the agent's
voice** and built for 20–45 seconds of vertical video. Works for a listing tour, a tip, or a market
take. Draft-only.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent gives a topic or pastes a listing's details
  ✓ Loads the agent's voice so the talking lines + caption sound like them
  ✓ Output: hook + scene-by-scene script (VO + on-screen text + B-roll) + a suggested caption
SUPERCHARGED (when connected)
  + get_my_profile: brand voice, brokerage, areas, niche, default hashtags
  + Canva / Figma: turn on-screen text frames + a cover/thumbnail into branded graphics
```

## Load first (every time)

1. **Load the agent's voice.** Call `get_my_profile` first for brand voice + business info. If
   unavailable or "No profile configured," fall back to `voice-profile.md` and `CLAUDE.md`; if neither
   exists, use a warm, energetic default and suggest **learn-my-voice**.

- **`voice-profile.md`** — tone, rhythm, catchphrases, emoji/hashtag habits, few-shot examples. The
  spoken lines should sound like how *they* actually talk on camera.
- **`CLAUDE.md`** — brokerage, sign-off, default hashtags, "don't" rules.

## Fair Housing guardrail

Script the **home, the tip, or the market fact** — never the buyer or who lives in an area. No
steering or targeting in the VO or on-screen text ("perfect for families," "safe/exclusive
neighborhood," "great starter home for newlyweds"). Use **primary bedroom**, not "master." Describe
the property, not the buyer. Don't script unverified claims or numbers — mark unknowns as
`[fill in: …]` and keep any stat cited per **market-update-post** rules.

## Format rules for short-form

- **Hook in the first 1–2 seconds** — a question, a bold claim, or a pattern interrupt; assume no
  sound and a fast scroll.
- **20–45 seconds total**, 4–7 scenes; one idea per scene; keep VO tight (people read captions).
- **On-screen text** for every scene (most viewers watch muted) — short, punchy.
- **B-roll/shot notes** so it's actually filmable (what to point the camera at).
- **Caption + hashtags** that work for Reels/TikTok/Shorts; a clear ask (DM, link in bio, "follow for
  more [area] real estate").

## Output format

```markdown
# Reel / Short Script — [topic or address]
**Target length:** ~[30] sec · **Format:** vertical 9:16

---

## Hook (0–2s)
🎙️ VO: [the spoken hook]
📝 On-screen: [big bold text]
🎬 Shot: [what's on screen]

## Scene 2
🎙️ VO: […]
📝 On-screen: […]
🎬 Shot: […]

## Scene 3 … (repeat for 4–7 scenes total; build to the payoff)

## Close / CTA
🎙️ VO: [call to action in their voice]
📝 On-screen: [CTA text]
🎬 Shot: [agent on camera / contact card]

---

## Suggested caption
[Caption in voice — first line is a second hook, then context, then CTA.]
Hashtags: #[...] (Reels/TikTok-friendly mix of niche + local)

🎨 Visual idea: [cover/thumbnail + on-screen text frames you could build in Canva/Figma]

[Sign-off from the profile]
```

## Execution flow

1. **Parse the request** — get the topic or listing details and any target length/platform. If
   nothing's given, ask what the video is about.
2. **Load voice + business** — `get_my_profile` first; fall back to the saved files.
3. **Find the hook + arc** — one core idea, a 1–2s hook, and a 4–7 scene build to a payoff/CTA.
4. **Write the script** — per scene: VO (in their spoken voice), on-screen text, and a filmable shot
   note. Keep it within the target length.
5. **Write the caption + hashtags** and a visual idea for the cover/on-screen frames.
6. **Self-check** — Fair Housing clean (property/tip, not buyer); any stat cited; placeholders for
   unknowns.
7. **Output** using the template.
8. **Offer the next step** — build cover/text frames in **Canva**/**Figma** if connected (else leave
   the visual idea); or run **social-repurpose** to pair the video with feed/Story posts.

## Related skills

- **listing-copy** — source description for a listing-tour reel.
- **market-update-post** — sourced stats to script a "market in 30 seconds" reel.
- **social-repurpose** — surround the video with native feed + Story posts.
- **content-calendar** — slot reels into the posting cadence.
- **learn-my-voice** — builds/updates the `voice-profile.md` this skill writes from.
