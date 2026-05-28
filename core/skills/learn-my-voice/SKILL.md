---
name: learn-my-voice
description: >
  Learn the agent's writing voice so every piece of content sounds like them. Use when the agent
  says "learn my voice", "set up my voice", "this doesn't sound like me", "train on my writing",
  "make it sound like me", or pastes/links samples of their captions, listings, or emails. Builds
  and refines a reusable voice profile that all content skills draw on.
metadata:
  version: "1.0.0"
---

# Learn My Voice

Capture how the agent actually writes so listings, social captions, emails, and lead replies sound
like *them*, not generic AI. Produces a reusable `voice-profile.md` that every content skill reads.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How the profile is delivered

There are two ways the voice profile reaches the content skills:

- **Self-serve (this skill):** build and refine `voice-profile.md` in the agent's working folder
  (the flow described below). The profile lives locally and every content skill reads it.
- **Server-hosted (managed/agency clients):** for STOYC-managed clients the voice profile is
  typically set **server-side** and delivered via the `~~crm` connector's `get_my_profile` tool, so
  the client does nothing and there are no local files. Skills can read voice + business context
  straight from `get_my_profile`.

This skill is for **building or refining** that profile — either self-serve, or to generate a
profile that STOYC then loads server-side for a managed client. Use it either way.

## How it works

```
ALWAYS (works standalone)
  ✓ Agent pastes samples (best captions, a listing, a couple of emails, their bio)
  ✓ Output: a structured voice profile + a library of their real writing samples
SUPERCHARGED (when connected)
  + Google Drive / Gmail: pull existing listings, newsletters, sent emails as samples
```

## Inputs to gather

Ask for (or pull from Drive/Gmail) 8–20 real samples that show range:
- 3–5 of their best social captions
- 1–2 listing descriptions
- 2–3 client emails (a lead reply, a follow-up)
- Their bio / "about me"

If they have a brand/style guide, ingest it too.

## Output: `voice-profile.md`

Save to the agent's **selected working folder** (in Cowork, always write to the working folder, not a
relative path). If no working folder is selected, ask them to pick one first.

```markdown
# Voice Profile — [Agent Name]

## I Am / I Am Not
| I Am | I Am Not |
|------|----------|
| warm, local-expert, plain-spoken | corporate, salesy, jargon-heavy |
| ... | ... |

## Tone by context
| Context | Tone |
|---------|------|
| Luxury listing | elevated, restrained, evocative |
| First-time buyer DM | reassuring, simple, encouraging |
| Market update | confident, data-backed, brief |

## Signature conventions
- Emoji: [usage rule]
- Hashtags: [their typical set]
- Sign-off: "[their sign-off]"
- Formatting: [short paragraphs? bullet CTAs?]

## Audience & niche
- [who they serve, what market]

## Real writing samples (word-for-word — the most important section)
> [Paste 5–8 of their actual best posts/snippets here, unedited. Content skills mimic these.]

## Fair Housing guardrail
Never use language that steers or references protected classes (race, religion, familial status,
national origin, disability, sex). Describe the **home and features**, never the **buyer or
neighborhood demographics**.
```

## Execution flow

1. Confirm a working folder is selected (needed to persist the profile).
2. Collect samples (paste or pull from Drive/Gmail).
3. Analyze for: recurring phrases, sentence length/rhythm, formality, emoji/hashtag/sign-off habits, how tone shifts by context.
4. Draft `voice-profile.md` using the template, **quoting real examples verbatim** in the few-shot section (this drives fidelity far more than adjectives).
5. Show the agent the "I Am / I Am Not" table + 1–2 sample outputs written in their voice; ask "does this sound like you?"
6. Refine from feedback. Save the file. Confirm it's saved and that all content skills will now use it.

## Refinement loop

When the agent later says "that doesn't sound like me", ask for 2–3 more examples of the right voice,
update the few-shot section and conventions, and re-save. The profile improves over time.

## Used by

Every content-generating skill loads this profile first: `lead-reply`, `follow-up`, and (in other
packages) listing copy, social posts, newsletters, blogs, bios. Operating rules/business facts live
separately in `CLAUDE.md` via the **remember** skill.
