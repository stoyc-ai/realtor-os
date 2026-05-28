---
name: review-response
description: >
  Draft professional, on-brand replies to Google reviews and write review-request outreach for the
  agent to send happy clients. Use when the agent says "respond to this review", "reply to my
  reviews", "review responses", "draft a review reply", "ask clients for reviews", or "get me more
  google reviews". Reads reviews via the ~~gbp connector when connected (else the agent pastes the
  review). Posting a reply is a WRITE — it always drafts, shows, and confirms first.
metadata:
  version: "1.0.0"
---

# Review Response

Reviews help you get found on Google and win trust — but a tone-deaf reply (especially to a bad one)
does real damage. This skill drafts replies that sound like *the agent*, handles good and bad reviews
very differently, and writes messages the agent can send happy clients to ask for a review. It
**drafts first, shows the reply, and posts via `~~gbp` only after the agent says yes**. Without the
connector, the agent pastes in the review and copies the reply into their Google Business Profile.

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
  ✓ Agent pastes a review (or asks for review-request templates)
  ✓ Loads voice + business via get_my_profile so replies sound like them
  ✓ Output: a tailored on-brand reply + 2–3 review-request message templates
SUPERCHARGED (when connected)
  + ~~gbp: read the agent's recent Google reviews to reply to, and — only after confirmation —
    publish the reply (a WRITE)
```

## Load first (every time)

1. **Load voice + business.** Call `get_my_profile` first — brand voice plus business facts (name,
   brokerage, sign-off). Replies are public and represent the brand, so match their tone. Fallback:
   `voice-profile.md` + `CLAUDE.md`; if neither, use a warm, professional default.

## Fair Housing + policy guardrails

- **Replies are public.** Keep them about the **service and experience** — never reference a
  reviewer's or area's protected characteristics, and never steer.
- **Privacy.** Don't disclose private transaction details (address, price, the client's situation) in
  a public reply, even if the reviewer did — keep it general and gracious.
- **Never fabricate reviews**, never offer incentives in exchange for reviews, and never post fake or
  AI-written reviews. Follow **Google's review policies**: you may *respond* to and *request* reviews,
  but the review itself must come from a real client of their own accord.

## Replying: positive vs. negative

```
POSITIVE / NEUTRAL review
  ✓ Thank them by name (if shown), reference one specific thing they mentioned, reinforce the brand,
    light invitation to refer/return. Short and genuine — not a copy-paste "Thank you!".

NEGATIVE / CRITICAL review
  ✗ Never argue, never get defensive, never blame the client, never disclose private details.
  ✓ Acknowledge + empathize, apologize for the experience (not necessarily fault), stay calm and
    professional, and ALWAYS take it offline: invite them to connect directly to make it right
    (provide a contact path). Brief is better — don't relitigate publicly.
  ⚠ If the review looks fake / not a real client / violates Google policy, don't engage emotionally;
    note the agent can flag it to Google for removal and draft a neutral, minimal holding reply.
```

Never invent details about the interaction. If the reply needs a fact only the agent knows, use a
`[fill in: …]` placeholder.

## Output format

```markdown
# Review Response — [Reviewer name / “pasted review”] · [★ rating] · [date]
**Voice:** [source] · **Connector:** [~~gbp connected → can post on confirm | not connected → paste into GBP]

**Review:** "[the review text]"
**Read as:** [Positive / Neutral / Negative — tone, what they're really saying]

---

## Reply (ready to post)
[The reply in the agent's voice — public-safe, no private details. Positive: specific + grateful.
Negative: empathetic, non-defensive, invites offline resolution with a contact path. Sign-off if it
fits their style.]

**Alternative (warmer / more concise):** [one short variant so they can pick the tone]

---
[If ~~gbp connected:]
**Post this reply to the review on your Google Business Profile?** Posting is a write — I won't
publish until you confirm.

[If not connected:]
**To use this:** open Google Business Profile → Reviews → Reply → paste the above.
```

For a **review-request** request, output instead:

```markdown
# Review Request Templates — for [happy clients / post-close]
> Only request reviews from real clients. Don't offer incentives. Send your GBP "leave a review" link.

**1. Text / DM (post-close):** [short, warm, 2–3 lines, in their voice, with [review link]]
**2. Email:** [subject + a few warm lines + [review link] + sign-off]
**3. In-person / verbal script:** [one or two natural lines they can say]
```

## Execution flow

1. **Parse the request** — reply to a review vs. generate review-request templates (or both).
2. **Load voice + business** — `get_my_profile` first; fall back to `voice-profile.md` / `CLAUDE.md`.
3. **Get the review** — read recent reviews via `~~gbp` if connected (let the agent pick which one);
   otherwise ask the agent to paste the review text + rating.
4. **Classify** the review (positive/neutral/negative) and read what it's really saying.
5. **Draft the reply** in their voice — positive: specific + grateful; negative: empathetic,
   non-defensive, offline invitation with a contact path. Add one tone-alternative. Apply Fair Housing
   + privacy + Google-policy guardrails. (Or write the 2–3 review-request templates.)
6. **Show the draft first** using the template — never post silently.
7. **Confirm before posting** (connected): ask "Post this reply?" and publish via `~~gbp` **only on
   explicit yes**. Not connected → give paste-into-GBP steps.
8. **Echo back** what was posted (or that nothing was).

## Write safety

- **Posting a reply via `~~gbp` is a WRITE** — draft, show, and post only on explicit confirmation.
  No confirmation → write nothing.
- **Never fabricate reviews** or write a review on a client's behalf; never offer incentives for
  reviews. Follow Google's review policies.
- **No private details** in public replies; never argue with or blame a reviewer.
- Echo exactly what was published after writing.

## Related skills

- **gbp-manager** — broader Google Business Profile posts + optimization (also uses `~~gbp`).
- **lead-reply** — for private 1:1 replies to leads (this skill is for *public* review replies).
- **learn-my-voice** — refines the `voice-profile.md` the replies are written from.
- **seo-audit** — reviews/ratings feed local SEO; this skill helps grow and manage them.
