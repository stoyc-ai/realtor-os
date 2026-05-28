---
name: blog-seo
description: >
  Write an SEO- and AI-search (GEO) optimized blog post in the agent's voice — the kind seo-audit
  recommends — targeted to their market and niche. Use when the agent says "write a blog post", "blog
  about [topic]", "SEO article", "write an article for my site", or "content for my website". Loads
  their voice + area, researches the topic (cited), and returns a title + meta description + article
  with H2s, an FAQ section, and a JSON-LD schema note — ready to paste into Webflow or WordPress.
metadata:
  version: "1.0.0"
---

# SEO + AI-Search (GEO) Blog Post

Write a genuinely useful, search-optimized blog post that ranks on Google **and** gets cited by AI
answer engines (ChatGPT, Perplexity, Google AI Overviews, Gemini, Claude) — in *the agent's* voice,
about *their* market. This is the content engine behind a **seo-audit**: it produces the citable,
entity-clear, FAQ-rich articles that audit recommends. Output is paste-ready for Webflow or WordPress.

## How it works

```
ALWAYS (works standalone)
  ✓ Agent gives a topic ("blog about first-time buyer down payments in [area]")
  ✓ Loads voice + area/niche via get_my_profile (fallback voice-profile.md / CLAUDE.md)
  ✓ Web research on the topic, each fact cited
  ✓ Output: title + meta description + article (H2s) + FAQ section + JSON-LD schema note
SUPERCHARGED (when connected)
  + Webflow / WordPress: format for the CMS (and note where the FAQ + schema go)
  + Ahrefs: validate the target keyword + related terms instead of inferring
  + web search: current local stats so the post has real, cited data (topical authority)
```

## Load first (every time)

1. **Load the agent's voice + market.** Call `get_my_profile` first — brand voice (tone, examples,
   sign-off) + business info (brokerage, **service areas**, target neighborhoods, **niche**, name).
   Write in their voice and target their real market (e.g. "Willow Glen homes for sale"), not generic
   advice. If unavailable / "No profile configured," fall back to `voice-profile.md` and `CLAUDE.md`;
   if neither exists, ask for voice + area/niche.

## GEO principles (apply throughout — from seo-audit)

This is what makes the post citable by AI engines, not just rank on Google:

- **Citable facts** — write clear, factual, self-contained statements an AI can lift verbatim ("The
  median down payment for first-time buyers in [area] is [X]"). Back numbers with a cited source.
- **Entity clarity** — state plainly and consistently who the agent is, where they serve, and what
  they specialize in. Tie the post back to the agent as the local authority.
- **FAQ content** — a real FAQ section answering the actual questions buyers/sellers ask; AI engines
  pull these directly into answers. Phrase headings as natural questions.
- **Schema** — include a `FAQPage` (and where relevant `Article` / `BlogPosting`) JSON-LD note so
  engines extract facts reliably.
- **Topical authority** — depth on their specific neighborhoods/markets with real, named local data.
- **One clear H1, descriptive H2s** — scannable structure for both crawlers and LLMs.

## Research the topic (cite everything)

```
1. Web search the topic for current, accurate info + any local data for their area.
2. Prefer authoritative sources (gov/agency, local MLS/association, Realtor.com, Redfin, FRED).
3. Cite each stat/claim inline (source name + date). Never invent figures.
4. Unverifiable detail → [fill in: …] placeholder, not a guess.
```

## Fair Housing guardrail

Describe **homes, neighborhoods (by objective features), the market, and the process** — never the
**people** or who lives somewhere. No steering: no "great for families," "safe area," "good schools
for people like you," and no reference to or implication of protected characteristics. When writing
about a neighborhood, use objective facts (price, amenities, commute, named places) that serve every
reader equally.

## Output format

```markdown
# [Working title]
**Voice:** [source] · **Target:** [keyword + location] · **For:** [Webflow / WordPress / paste-ready]

## SEO meta
- **Title tag (≤60 chars):** [keyword + location]
- **Meta description (≤155 chars):** [benefit + location + soft CTA]
- **URL slug:** /[kebab-case-slug]
- **Primary keyword:** [term] · **Secondary:** [terms]

---

# [H1 — matches intent, includes location/keyword naturally]

[Intro — 2–3 sentences. Answer the core question up front (good for AI snippets), set up the post.]

## [H2 — descriptive subtopic]
[Body in their voice. Include a citable, self-contained fact with *[source, date]*.]

## [H2 — subtopic]
[Body — local data, named places, the agent's POV as the area expert.]

## [H2 — subtopic]
[Body…]

## Frequently asked questions
**[Real question buyers/sellers ask, phrased naturally]?**
[Concise, self-contained answer an AI can quote. Cite if it includes a stat.]

**[Question]?**
[Answer.]

**[Question]?**
[Answer.]

[Closing — restate the agent as the local go-to + one clear CTA (call, valuation, search).]
[Sign-off / byline tying to the agent + brokerage — reinforces the entity.]

---

## Suggested JSON-LD schema (paste into <head> or CMS custom code)
\`\`\`json
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    { "@type": "Question", "name": "[FAQ question]",
      "acceptedAnswer": { "@type": "Answer", "text": "[FAQ answer]" } }
  ]
}
\`\`\`
> Also consider a `BlogPosting`/`Article` block with `author` = the agent (`Person`) and
> `publisher` = the brokerage (`Organization`) to reinforce entity + E-E-A-T.

**Paste guide:** Title tag + meta in the [Webflow/WordPress] page SEO settings; body as the post;
FAQ as its own section; JSON-LD in the page's custom code/head.
```

If Webflow/WordPress is connected, format for that CMS and note where the meta, body, FAQ, and schema
each go. If not connected, output paste-ready and say so.

## Execution flow

1. **Parse the request** — get the topic; infer/confirm target keyword + location from the profile.
2. **Load voice + market** — `get_my_profile`; fall back to `voice-profile.md` / `CLAUDE.md`.
3. **Research** — web search the topic + local data; capture source + date for every fact.
4. **Draft the article** — title + meta, H1, descriptive H2s, citable facts, real local depth, a
   natural-question FAQ, and a close that reinforces the agent as the local entity — in their voice.
5. **Apply GEO** — self-contained citable statements, entity clarity, FAQPage schema note.
6. **Format for the CMS** — Webflow/WordPress placement notes (or paste-ready).
7. **Output the draft** using the template. Honor the Fair Housing guardrail throughout.
8. **Offer next steps** — e.g. "Want a matching social post (**social-repurpose**) or to run a fresh
   **seo-audit** after this publishes?"

## Write safety

- **Drafts only** — produces content + schema for the agent to publish; never publishes on its own.
- Every stat carries a cited source + date; unverifiable detail becomes `[fill in: …]`.
- Ethical SEO only — no keyword stuffing, cloaking, or fabricated facts/reviews.

## Related skills

- **seo-audit** — diagnoses the site and recommends exactly this kind of post (start there).
- **single-property-page** — listing/landing page copy with its own schema.
- **agent-bio** — strengthens the About page for E-E-A-T and entity clarity.
- **social-repurpose** — turn the post into social content.
- **learn-my-voice** — builds the `voice-profile.md` this skill writes from.
