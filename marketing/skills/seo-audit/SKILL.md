---
name: seo-audit
description: >
  Audit a real estate agent's website for getting found — on Google AND in AI assistants (ChatGPT,
  Perplexity, Google's AI answers, Gemini, Claude) — and explain, in plain English, what's costing
  them leads and exactly what to fix. Use when the agent says "audit my website", "SEO audit", "how's
  my site for SEO", "check my site for AI search", "improve my Google ranking", "why am I not ranking",
  or gives a website URL. Reads the live site and tailors everything to their market.
metadata:
  version: "1.1.0"
---

# Website Audit — Getting Found on Google & AI (for real estate agents)

Look at the agent's website the way Google and AI assistants do, then tell them — **in plain English,
like a smart friend who does this for a living** — what's quietly costing them leads and exactly what
to do about it. This is written for a busy agent, not a web developer.

## The golden rule: write for the agent, not for a developer

The agent is a real estate professional, not a coder. So:

- **Lead with the money/lead impact, not the technical term.** Not "missing JSON-LD schema" → instead "Google and AI assistants can't tell who you are or where you work, so they don't recommend you. Here's the fix."
- **Explain or avoid jargon.** If you must use a term, define it in 5 words: *SEO = showing up on Google. AI search = showing up when someone asks ChatGPT. Schema = code that tells Google/AI exactly who you are. NAP = your name, address & phone matching everywhere.*
- **Tag every fix with who does it + how hard.** Use these tags so the agent knows what's on them vs. what to hand off:
  - `[✅ You — ~5 min]` something they can do themselves today (e.g. finish their Google Business Profile, add a headshot + bio, collect a testimonial).
  - `[🤝 We can do it]` STOYC/marketing can handle it (copy, pages, content).
  - `[🧑‍💻 Web person — ~30 min]` a developer/web-platform task (code, redirects, speed). Put the actual code in the appendix so the agent just forwards it.
- **Always connect to a real outcome:** more calls, more leads, showing up for "[their area] homes for sale," getting named when a buyer asks an AI "who's a good agent in [area]?"

## How it works

```
ALWAYS (works standalone)
  ✓ Agent gives their website URL (or it's in their profile)
  ✓ Read the homepage + key pages, then explain what's costing leads and what to fix — in plain English
SUPERCHARGED (when connected)
  + get_my_profile: target their real areas, neighborhoods, niche, name, brokerage
  + Ahrefs / SimilarWeb: real traffic/keyword numbers instead of estimates
  + Google Business Profile (~~gbp): check their Google listing is complete + consistent
```

## Before you start

1. **Load context.** Call `get_my_profile` for the agent's **name, brokerage, service areas, target neighborhoods, and niche** (fallback `CLAUDE.md`). Every recommendation should name *their* real market (e.g. "show up for 'Willow Glen homes for sale'"), not generic advice. If unknown, ask 1–2 quick questions.
2. **Get the URL** if it's not provided or saved.
3. **Read the site** (homepage, About, area/neighborhood pages, listings/home-search, contact, blog). Only describe what's actually there — never invent pages or numbers.

## What to look at (translate each into plain agent language)

For each, find the issue, say **why it costs them leads**, and give a tagged fix.

- **Do you show up on Google for your area?** Page titles, the words on the page, and whether you have pages for the neighborhoods you farm. *(Most agents only rank for their own name — not "homes for sale in [area]".)*
- **Your Google listing (local).** Is the Google Business Profile complete and does the website's name/address/phone match it? This drives the map results and "near me" searches. `[✅ You]` mostly.
- **Can buyers/sellers actually do something?** Home search/IDX, a clear "contact" or "book a call," your phone, lead capture. A pretty site that doesn't capture leads is a leak.
- **Do you look trustworthy?** Your photo, a real bio, credentials, named client testimonials, recent sales. Google and AI both favor real, provable expertise.
- **Will AI assistants recommend you?** (The new search — explain it.) More buyers/sellers ask ChatGPT, Perplexity, and Google's AI "who's a good agent in [area]?" To get named, your site needs clear, factual statements about who you are and where you work, an FAQ that answers real buyer/seller questions, and the behind-the-scenes "schema" code so AI can read you. Most agent sites are invisible here — big opportunity.
- **The technical basics.** Site speed (slow = lower ranking + lost visitors), mobile, broken/missing pages, and that Google can find everything. Keep these brief and hand them off `[🧑‍💻 Web person]`.

## Output format

```markdown
# Your Website Audit — [site] · [date]

## The bottom line
[2–3 plain sentences. Grade in human terms, e.g. "Your site looks great to people, but Google and
AI assistants barely know you exist — which means you're missing leads from search. The good news:
the biggest wins are a few one-time fixes, and most don't need a developer."]

Found-on-Google: [🟢/🟡/🔴 + one phrase]  ·  Google listing (local): [..]  ·  AI assistants: [..]

## What's costing you leads right now
1. **[Plain-English problem]**
   - What's happening: [plain, cite what you saw]
   - Why it costs you: [leads/calls/visibility — concrete]
   - Fix: [specific action] — `[✅ You — 5 min]` / `[🤝 We can do it]` / `[🧑‍💻 Web person]`
2. …(top 3–5)

## Quick wins you can do this week (no developer needed)
- [ ] [✅ thing], e.g. "Finish your Google Business Profile — add hours, service areas, 10 photos, and your website link."
- [ ] [✅ thing], e.g. "Add your headshot + a 3-sentence bio that says who you are and the areas you serve."
- [ ] [✅ thing], e.g. "Ask 3 recent clients for a 2-sentence testimonial and add them with names."

## Get recommended by AI (the new way people search)
[Plain explanation + whether they currently show up. Then 3–5 specific moves: e.g. "Add an FAQ
answering the questions buyers actually ask in [area]" with example questions; "State plainly on your
homepage who you are and where you work"; "We'll add the behind-the-scenes code so AI can read you."]

## To hand to your web person
A short, plain checklist of the technical items (speed, sitemap, page setup, the code blocks in the
appendix). Frame it as "forward this section to whoever manages your website."

## Appendix — ready-to-paste code (for the web person)
[Only here: sitemap/robots.txt guidance and JSON-LD schema blocks, fully filled in with the agent's
real info. Clearly labeled so the agent can forward it without understanding it.]

## Want us to handle it?
[Offer the obvious next steps tied to other skills — see Related skills.]
```

## Execution flow

1. Load profile (name/areas/niche) + the URL.
2. Read the homepage + key pages; note what exists (cite URLs, dates).
3. If Ahrefs/SimilarWeb connected, pull real numbers; else say estimates are qualitative.
4. Find the issues, then **rewrite every one in plain agent language** with the lead/business impact and a who-does-it tag.
5. Put the top 3–5 lead-costing issues first; collect the easy DIY items into "Quick wins"; move all code into the appendix.
6. Fill schema/code blocks with the agent's **real** info (confirm anything uncertain — e.g. exact name — before putting it in code).
7. End with concrete next steps they can say yes to.

## Guardrails

- Only audit the agent's **own** site + public info. Cite what you saw; never invent pages, numbers, or facts. Flag anything uncertain (e.g. a name pulled from another source) and ask before publishing it in code.
- Recommend **ethical** SEO only — no fake reviews, no tricks.
- Any sample copy follows **Fair Housing**: describe the homes and areas, never target or describe people by protected class.
- Keep it skimmable and encouraging — the agent should finish feeling "I know exactly what to do next," not overwhelmed.

## Related skills

- **agent-bio** — write the About page / bio this audit recommends (E-E-A-T + AI clarity).
- **blog-seo** — write the FAQ and blog content that gets you cited by Google and AI.
- **single-property-page** — optimized listing/landing pages.
- **gbp-manager** — fix and optimize the Google Business Profile (the local-search side).
