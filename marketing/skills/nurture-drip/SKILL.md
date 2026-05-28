---
name: nurture-drip
description: >
  Write a multi-email nurture / drip sequence in the agent's voice for a specific audience (new buyer
  lead, seller lead, past client, open-house follow-up, etc.). Use when the agent says "nurture
  sequence", "drip campaign", "email series for [audience]", "write a follow-up sequence", or "set up
  emails for my new leads". Returns a labeled sequence with send timing per email (Day 0/2/5/10…),
  each with subject + body, formatted to load into Mailchimp or Klaviyo. Drafts only — never sends.
metadata:
  version: "1.0.0"
---

# Lead Nurture / Drip Sequence

Build a complete, on-brand email sequence that warms a lead (or stays in touch with a past client)
over days or weeks — written in *the agent's* voice, with the send cadence mapped out. This is a
**draft-only** skill: it writes the whole series and hands it back, ready to drop into Mailchimp or
Klaviyo as an automation. It never sends.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent names the audience ("new buyer leads", "past clients", "seller leads")
  ✓ Loads voice via get_my_profile (fallback voice-profile.md / CLAUDE.md)
  ✓ Output: a labeled sequence — N emails, each with Day, subject, body
SUPERCHARGED (when connected)
  + Mailchimp / Klaviyo: format as an automation/flow with merge tags + timing per step
  + web search: a current local stat or resource to anchor a "value" email (cited)
```

## Load first (every time)

1. **Load the agent's voice + business.** Call `get_my_profile` first — brand voice (tone, examples,
   sign-off, hashtags) + business info (brokerage, service area, niche, rules). Write in this voice.
   If unavailable / "No profile configured," fall back to `voice-profile.md` and `CLAUDE.md`; if
   neither exists, ask.

- **`voice-profile.md`** — match tone and the few-shot examples; if missing, warm + plain-spoken and
  suggest **learn-my-voice**.
- **`CLAUDE.md`** — honor sign-off, response-time/cadence rules, and any "don't" rules (e.g. don't
  over-email, don't cold-text).

## Pick the sequence shape

```
Identify the audience, then choose a goal + cadence:
  - new-buyer-lead   → goal: book a buyer consult / get them touring. ~5 emails over ~2 weeks.
  - seller-lead      → goal: book a listing appointment / home-value convo. ~5 emails over ~3 weeks.
  - past-client      → goal: stay top-of-mind for referrals/repeat. ~4 emails, slower (monthly-ish).
  - open-house / event follow-up → goal: re-engage attendees. ~3 emails over ~1 week.
Default cadence: Day 0, 2, 5, 10, 21 (front-loaded, then spaced). Adjust to the audience + any
cadence rule in CLAUDE.md.
```

Each email earns its place: lead with value (a tip, a stat, a resource), build trust, and make **one**
clear, low-pressure ask. Vary the angle so it doesn't read like five versions of "just checking in."

## Fair Housing guardrail

Describe **homes, the market, and the process** — never the **person** or who lives in an area. No
steering language and no reference to or implication of protected characteristics (race, color,
religion, national origin, sex, familial status, disability). "Great schools for families like
yours" / "safe area" / "perfect for a young couple" are out. Keep it about price, process, timing,
and objective local data — content that serves every recipient equally.

## Output format

```markdown
# Nurture Sequence — [Audience] · goal: [goal]
**Voice:** [source] · **Cadence:** Day [0/2/5/10/21] · **Format:** [Mailchimp / Klaviyo / paste-ready]

---

## Email 1 — Day 0 · [purpose, e.g. "Welcome + set expectations"]
**Subject:** [option]  *(alt: [shorter option])*
**Preview:** [~40–90 chars]

[Greeting in their voice — e.g. "Hi {{FNAME}},"]

[Body — short paragraphs, in their voice. Deliver one piece of value, make one clear next step.]

[Sign-off from CLAUDE.md]

---

## Email 2 — Day 2 · [purpose]
**Subject:** [option]
**Preview:** [text]

[Body…]

[Sign-off]

---

## Email 3 — Day 5 · [purpose]
…

## Email 4 — Day 10 · [purpose]
…

## Email 5 — Day 21 · [purpose — re-engage / clear final CTA]
…

---

**Automation setup:** In [Mailchimp/Klaviyo], create a [flow/automation] triggered when a contact
enters the "[audience]" segment; add each email above as a step with the Day delay shown. Merge tags:
`{{FNAME}}` (Mailchimp) / {{ first_name }} (Klaviyo).
```

If Mailchimp/Klaviyo is connected, format with that platform's merge tags and describe the trigger +
delays as automation steps. If not connected, output paste-ready text and note where each goes.

## Execution flow

1. **Parse the request** — identify the **audience** and any specified length/cadence/goal.
2. **Load voice + rules** — `get_my_profile`; fall back to `voice-profile.md` / `CLAUDE.md`.
3. **Choose the shape** — goal + email count + cadence for that audience (default Day 0/2/5/10/21).
4. **Optional research** — web search one current, cited local stat or resource for a value email.
5. **Draft the sequence** — each email gets a Day, purpose label, subject (+ alt), preview, and body
   in their voice, one clear ask each, honoring the sign-off and Fair Housing guardrail.
6. **Format for the platform** — automation steps + merge tags (Mailchimp/Klaviyo) or paste-ready.
7. **Output the draft** using the template. Do **not** send.
8. **Offer next steps** — e.g. "Want a matching SMS line per step, or a different audience next?"
   Only act on confirmation.

## Write safety

- **Drafts only** — never sends, schedules, or enrolls a contact in a flow.
- Respect any cadence / "don't over-email" rule in `CLAUDE.md`; don't stack more touches than asked.
- Keep merge tags intact; any local stat used in a value email must be cited with source + date.

## Related skills

- **lead-reply** — for a one-off reply to a specific lead (this skill is the automated series).
- **follow-up** — for nudging a single contact who's gone quiet, off-sequence.
- **newsletter** — recurring broadcast to the whole list (vs. an audience-triggered drip).
- **learn-my-voice** — builds the `voice-profile.md` this skill writes from.
