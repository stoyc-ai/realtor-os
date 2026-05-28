---
name: newsletter
description: >
  Write the agent's monthly market/email newsletter in their own voice, grounded in current local
  market stats. Use when the agent says "write my newsletter", "monthly email", "market newsletter",
  "send out my market update", or "draft this month's email to my list". Pulls their voice + service
  area, researches current cited local data, and returns subject-line options plus a sectioned email
  body formatted to paste into Mailchimp or Klaviyo. Drafts only — never sends.
metadata:
  version: "1.0.0"
---

# Monthly Market Newsletter

Turn "I need to email my list" into a finished, on-brand monthly newsletter — the kind a past client
actually opens — written in *the agent's* voice and backed by **current, cited** local market data.
This is a **draft-only** skill: it writes the email and hands it back, formatted to paste straight
into Mailchimp or Klaviyo. It never sends.

## How it works

```
ALWAYS (works standalone)
  ✓ Agent says "write my newsletter" (and optionally a focus, e.g. "spring sellers")
  ✓ Loads voice + service area via get_my_profile (fallback voice-profile.md / CLAUDE.md)
  ✓ Web search for current local stats, each fact cited
  ✓ Output: 2–3 subject lines + a sectioned email body in their voice
SUPERCHARGED (when connected)
  + Mailchimp / Klaviyo: format the body to paste in (or note where to drop each block)
  + web search: live median price, days-on-market, inventory for their real area
```

## Load first (every time)

1. **Load the agent's voice + market.** Call the `get_my_profile` tool first — it returns brand voice
   (tone, writing examples, sign-off, hashtags) + business info (brokerage, **service areas**, niche,
   rules). Write in this voice and target their real area. If it's unavailable or returns "No profile
   configured," fall back to `voice-profile.md` and `CLAUDE.md` in the working folder; if neither
   exists, ask the agent for their voice and market area.

- **`voice-profile.md`** — match tone, rhythm, emoji/hashtag habits, and the few-shot examples. If
  missing, write warm and plain-spoken and suggest running **learn-my-voice**.
- **`CLAUDE.md`** — honor the sign-off, brokerage facts, and any "don't" rules.

## Research the market (cite everything)

```
1. Pull current stats for their service area via web search:
   - median sale price + YoY/MoM trend
   - days on market
   - active inventory / months of supply
   - mortgage rate context (national 30-yr is fine if local isn't available)
2. Prefer recent, authoritative sources (local MLS/association, Realtor.com, Redfin, FRED).
3. Cite each stat inline as a source name + date. Never invent a number.
4. If a figure can't be verified, leave a [fill in: …] placeholder rather than guessing.
```

## Fair Housing guardrail

Describe **the market and the homes** — never the **people**. No steering: don't characterize an area
by who lives there ("great for families", "safe neighborhood", "up-and-coming"), and don't reference
or imply race, color, religion, national origin, sex, familial status, or disability. Talk about
price, inventory, rates, and timing — objective facts that serve every reader equally.

## Output format

```markdown
# [Month Year] Newsletter — [Service Area]
**Voice:** [profile / voice-profile.md / default] · **Format:** [Mailchimp / Klaviyo / paste-ready]

---

## Subject line options
1. [option — curiosity / benefit angle]
2. [option — stat / news angle]
3. [option — personal / warm angle]

**Preview text:** [~40–90 chars that complement the subject]

---

## Email body

**[Greeting in their voice — e.g. "Hey {{FNAME}},"]**

[Opening — 1–2 sentences, personal, sets up this month's theme.]

### 📊 [Service Area] market snapshot
- Median price: [$X], [up/down X%] [period] — *[source, date]*
- Days on market: [X] — *[source, date]*
- Inventory: [X / months of supply] — *[source, date]*
- Rates: [context] — *[source, date]*

[1–2 sentences translating what this means for a buyer AND a seller, plainly.]

### 🏡 [Section 2 — e.g. "What this means for you" / a tip / a featured listing]
[Short, useful, in their voice.]

### 💬 [Section 3 — soft CTA / question / value-add]
[One clear, low-pressure next step — reply, book a call, "what's my home worth?".]

[Sign-off from CLAUDE.md]

---

**Paste guide:** Drop subject + preview into the [Mailchimp/Klaviyo] campaign header; each `###`
section is a content block. Merge tags `{{FNAME}}` shown for Mailchimp ({{ first_name }} for Klaviyo).
```

If Mailchimp/Klaviyo is connected, format the body to paste in directly (and note the merge-tag
syntax for that platform). If neither is connected, output clean paste-ready text and say so.

## Execution flow

1. **Parse the request** — "write my newsletter", optional theme/focus, target month.
2. **Load voice + market** — call `get_my_profile`; fall back to `voice-profile.md` / `CLAUDE.md`.
3. **Research** — web search current stats for their real service area; capture source + date for each.
4. **Draft in their voice** — 2–3 subject lines + preview text, then a sectioned body (snapshot →
   meaning → soft CTA), honoring sign-off and the Fair Housing guardrail.
5. **Format for the platform** — Mailchimp/Klaviyo merge tags + a paste guide; clean text if no
   connector.
6. **Output the draft** using the template. Do **not** send.
7. **Offer next steps** — e.g. "Want me to spin a matching social post (try **social-repurpose**) or
   draft next month's?" Only act on confirmation.

## Write safety

- This skill **drafts only** — it never sends a campaign or schedules a send.
- Every market stat must carry a cited source + date; unverifiable figures become `[fill in: …]`.
- Keep merge tags intact so the agent's list personalization still works.

## Related skills

- **content-calendar** — schedule the newsletter alongside the month's other content.
- **social-repurpose** — turn the market snapshot into matching social posts.
- **market-update-post** — a shorter, social-first take on the same local data.
- **learn-my-voice** — builds the `voice-profile.md` this skill writes from.
