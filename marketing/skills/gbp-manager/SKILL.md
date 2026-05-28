---
name: gbp-manager
description: >
  Manage and optimize the agent's Google Business Profile — write GBP posts (offers, events, updates)
  and give concrete profile-completeness recommendations. Use when the agent says "google business
  post", "gbp post", "update my google profile", "optimize my google business profile", "post to my
  google profile", or "what's missing from my GBP". Posts/updates via the ~~gbp connector when it's
  connected (a confirmed write); otherwise drafts everything for the agent to paste into their GBP
  dashboard.
metadata:
  version: "1.0.0"
---

# Google Business Profile Manager

Your Google Business Profile (your free Google listing — the box with your name, reviews, and map pin
that shows up when people Google you) is one of the best ways to get found locally, and most agents
leave it half-built. This skill writes **profile posts** in the agent's voice and checks the profile
for what's missing, handing back specific fixes. When the **`~~gbp`** connector is connected it can
post/update directly — but posting goes live, so it always **drafts, shows, and confirms first**.
Without the connector it still works: it drafts the post and the to-do list for the agent to paste
into their Google Business Profile.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

> Note: throughout this skill, "GBP" means your Google Business Profile.

## How it works

```
ALWAYS (works standalone)
  ✓ Agent says what to post (offer/event/update) or asks what to fix on their profile
  ✓ Loads voice + business via get_my_profile so posts sound like them and name their market
  ✓ Output: GBP post drafts + a profile-completeness checklist — to paste into the GBP dashboard
SUPERCHARGED (when connected)
  + ~~gbp: read the current profile (categories, services, description, photos) to audit against real
    data, and — only after confirmation — publish a post or apply a profile update (a WRITE)
```

## Load first (every time)

1. **Load voice + business.** Call `get_my_profile` first — brand voice plus business facts
   (brokerage, service areas, niche, name, license/DRE #, hours). Posts use the voice; the audit uses
   the facts (correct categories, service areas, accurate description). Fallback: `voice-profile.md` +
   `CLAUDE.md`; if neither, ask.

## Fair Housing guardrail

GBP posts describe **homes, services, offers, and the local market** — never target or imply
preference by protected class, and never characterize the area by who lives there. An "offer" or
"event" post promotes the home/event, not an audience. Keep neighborhood mentions to objective facts.

## What this skill does

**A) GBP posts** (Google Business "What's new", Offer, and Event post types):
- **Update / What's new** — a market tip, a new listing, "just sold", a helpful FAQ answer.
- **Offer** — e.g. "Free home valuation this month" — include the offer + CTA + any dates/terms.
- **Event** — open house / seminar — title, start/end date & time, location, CTA.
- Keep posts tight (Google truncates ~1,500 chars but reads best short), one clear CTA button
  (Book / Call / Learn more / Sign up), and write in the agent's voice. Suggest one photo per post.

**B) Profile optimization** (audit + recommendations):
- **Categories** — correct primary ("Real estate agent") + relevant secondaries.
- **Services / service areas** — the agent's real farm areas and offerings listed out.
- **Business description** — 750 chars, keyword + location rich, in their voice, accurate.
- **Photos** — headshot, logo, listing/area photos; recommend a refresh cadence.
- **Hours, phone, website, booking link, attributes** — present and consistent (NAP matches site/GBP).
- **Posts cadence** — recommend posting weekly to keep the profile active.
- **Q&A** — seed common buyer/seller questions (the agent answers from their own account).

Never invent profile data. If the connector isn't reading the live profile, frame the audit as
"check/confirm these" rather than asserting what's there.

## Output format

```markdown
# Google Business Profile — [Post / Optimization / Both] · [date]
**Voice:** [source] · **Business:** [name · brokerage · areas]
**Connector:** [~~gbp connected → can post on confirm | not connected → paste into GBP dashboard]

---

## GBP Post draft   ← (when posting)
**Type:** [Update / Offer / Event]
**Post text:** [in the agent's voice, tight, one idea]
**CTA button:** [Book / Call / Learn more / Sign up] → [link]
**Offer/Event details:** [dates, terms, location — only if Offer/Event]
**Suggested photo:** [one line]

## Profile optimization   ← (when auditing)
**Completeness: [X / 10 areas strong]**
- ✅ [what's solid]
- 🔧 **[Field]** — *Now:* [current/“confirm”] → *Recommend:* [exact change, paste-ready]
- 🔧 **Business description (paste-ready):** "[full rewritten description in their voice]"
- 🔧 **Categories:** primary [X]; add secondary [Y, Z]
- …

---
[If ~~gbp connected:]
**Publish this post / apply these changes to your Google Business Profile?** Posting is a write —
I won't push anything until you say yes. Tell me which items to apply.

[If not connected:]
**To use this:** open your Google Business Profile dashboard → Add post (or Edit profile) → paste the
above. Connect ~~gbp later and I can post for you.
```

## Execution flow

1. **Parse the request** — a post (and which type) vs. an optimization audit vs. both.
2. **Load voice + business** — `get_my_profile` first; fall back to `voice-profile.md` / `CLAUDE.md`.
3. **Read the live profile** (connected) — pull categories, services, description, photos, hours via
   `~~gbp` to audit against real data; otherwise audit as "confirm these" and draft from profile facts.
4. **Draft the post and/or audit** — post in their voice with one CTA; audit with paste-ready fixes
   (especially a full rewritten business description). Apply the Fair Housing guardrail.
5. **Show everything first.** Output the draft/audit using the template — never publish silently.
6. **Confirm before any write** (connected): ask "Publish this post / apply these changes?" and write
   **only the items the agent approves**, via `~~gbp`. Re-confirm anything ambiguous (e.g. category
   changes). Not connected → give the paste-into-dashboard steps instead.
7. **Echo back** what was posted/updated (or that nothing was, if drafting only).

## Write safety

- **Posting and profile edits via `~~gbp` are WRITES** — draft first, show, and apply only on explicit
  confirmation. If the agent doesn't confirm, write nothing.
- **Never generate fake reviews** or post on a client's behalf as if from a customer. Reviews come
  from real clients only (see **review-response** for review requests and replies).
- Don't fabricate offers, dates, or business facts — placeholder anything unknown.
- Echo exactly what was published/changed after writing.

## Related skills

- **review-response** — reply to Google reviews and request reviews from real clients (also uses `~~gbp`).
- **seo-audit** — the website side of local SEO; cross-checks NAP consistency with GBP.
- **content-calendar** — schedule recurring GBP posts alongside social.
- **market-update-post** — turn a market stat into a GBP "What's new" post.
