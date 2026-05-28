---
name: seo-audit
description: >
  Audit a real estate agent's website for search ranking — both traditional Google SEO and AI-search
  (GEO: ChatGPT, Perplexity, Google AI Overviews, Gemini, Claude) — and return prioritized, specific
  fixes. Use when the agent says "audit my website", "SEO audit", "how's my site for SEO", "check my
  site for AI search", "improve my Google ranking", "GEO audit", "why am I not ranking", or gives a
  website URL to review. Reads the live site via web search and tailors fixes to their market.
metadata:
  version: "1.0.0"
---

# Website SEO + AI-Search (GEO) Audit

Crawl the agent's live website, diagnose what's holding back their ranking in **both** Google and
**AI search engines**, and hand them a prioritized punch list of specific, do-this fixes — tailored
to their brokerage, market, and niche. Think of it as the website equivalent of the Google Business
Profile audit.

## How it works

```
ALWAYS (works standalone)
  ✓ Agent gives their website URL (or it's in their profile)
  ✓ Read the homepage + key pages via web search, analyze SEO + GEO, return a graded report
SUPERCHARGED (when connected)
  + get_my_profile: target their real service areas, neighborhoods, niche, brokerage
  + Ahrefs / SimilarWeb: real keyword, backlink, and traffic metrics instead of qualitative reads
  + Google Business Profile (~~gbp): cross-check NAP + local consistency
```

## Before you start

1. **Load context.** Call `get_my_profile` for the agent's **brokerage, service areas, target
   neighborhoods, niche, and name** (fallback `CLAUDE.md`). Recommendations must target *their* real
   market (e.g. "Willow Glen homes for sale"), not generic advice. If unknown, ask.
2. **Get the URL.** If not provided or in the profile, ask: "What's your website address?"
3. **Read the site.** Use web search/fetch to read the homepage and the key pages you can find:
   About/agent bio, service-area/neighborhood pages, listings/IDX, blog, contact. Note what actually
   exists — never assume or invent pages.

## What to evaluate

Score each area (A–F) and find concrete issues. Be specific to what you observed on the site.

**1. Technical foundation** — HTTPS, mobile-friendliness, obvious speed issues, sitemap/robots, indexability, broken links, duplicate/thin pages, canonical/URL hygiene.

**2. On-page SEO** — page titles & meta descriptions (present? unique? keyword + location?), one clear H1 per page, heading structure, content depth, image alt text, internal linking, clear CTAs.

**3. Local SEO (highest leverage for agents)** — NAP (name/address/phone) present and consistent with their Google Business Profile; dedicated **location/neighborhood pages** for their farm areas; local keywords in titles/content; embedded map; reviews/testimonials on-site; links to/from GBP.

**4. Structured data (schema.org JSON-LD)** — this powers both rich results AND AI extraction. Check for / recommend: `RealEstateAgent` or `LocalBusiness`, `Person` (the agent), `Organization` (brokerage), `RealEstateListing`/`Product` for listings, `FAQPage`, and `Review`/`AggregateRating`. Most agent sites have none — flag it.

**5. E-E-A-T & trust** — real agent bio with credentials/experience, named team, testimonials, license #, clear contact, original local insight (not boilerplate). Google and AI engines both weight demonstrated expertise.

**6. AI-search / GEO** — how citable the site is to LLM answer engines:
   - **Clear, factual, self-contained statements** an AI can quote ("Jane Smith is a Compass agent specializing in first-time buyers in Willow Glen, San Jose").
   - **Entity clarity** — who they are, where they serve, what they specialize in, stated plainly and consistently.
   - **FAQ-style content** answering real buyer/seller questions ("How much do I need for a down payment in [area]?") — AI engines pull these directly.
   - **Schema** (above) so engines can extract facts reliably.
   - **Topical authority** — depth on their specific neighborhoods/markets, with real local data and named places.
   - **AI crawler access** — `robots.txt` shouldn't block `GPTBot`, `ClaudeBot`, `PerplexityBot`, `Google-Extended` if they want to be cited; consider an `llms.txt`.
   - **Off-site entity consistency** — same NAP/bio across GBP, Zillow/Realtor profiles, socials (reinforces the entity for AI).

**7. Conversion/UX** — obvious lead capture, IDX/home search, mobile CTAs, fast path to contact.

## Output format

```markdown
# SEO + AI-Search Audit — [website] · [date]

**Overall: [grade]** | Google SEO: [grade] · Local SEO: [grade] · AI-Search (GEO): [grade]

## Do these first (top 5)
1. **[Fix]** — [why it matters: SEO and/or AI search] — [exact action]
2. …

## Findings by priority

### 🔴 Critical
- **[Issue]** (Technical/On-page/Local/Schema/E-E-A-T/GEO)
  - *Observed:* [what's on the site, cited]
  - *Why:* [impact on Google and/or AI engines]
  - *Fix:* [specific, copy-paste where possible — e.g. suggested title tag, a JSON-LD snippet, FAQ Q&As]

### 🟠 High
- …

### 🟡 Medium / quick wins
- …

## Suggested additions (tailored to your market)
- Neighborhood pages to create: [their real areas]
- FAQ topics to add (great for AI search): [5–8 real buyer/seller questions for their market]
- Schema to add: [which types + a ready JSON-LD block]

## Notes
- Metrics are qualitative unless Ahrefs/SimilarWeb is connected.
- Based on pages reachable at audit time ([date]); verify before publishing changes.
```

## Execution flow

1. Load profile (areas/niche/brokerage) + get the URL.
2. Read homepage + key pages via web search; record what exists (cite URLs).
3. If Ahrefs/SimilarWeb connected, pull keyword/traffic/backlink metrics; else note qualitative.
4. Score the 7 areas; collect specific issues with *Observed / Why / Fix*.
5. Write ready-to-use fixes: suggested **title tags + meta descriptions**, **JSON-LD schema blocks**, and **FAQ Q&As** targeted to their real neighborhoods.
6. Render the report (top-5 first, then by priority). Offer next steps below.

## Guardrails

- Only audit the agent's **own** site + public info. Cite what you observed; don't fabricate metrics or pages.
- Recommend **ethical** SEO only (no cloaking, keyword stuffing, fake reviews).
- Any sample content must follow **Fair Housing** (describe homes/areas, never target protected classes).
- Be concrete: every finding gets an action the agent (or their web person) can actually do.

## Related skills

- **blog-seo** — write the SEO/GEO blog posts and FAQ content this audit recommends.
- **single-property-page** — build optimized listing/landing pages.
- **agent-bio** — strengthen the About page for E-E-A-T and entity clarity.
- **gbp-manager** — fix the Google Business Profile side of local SEO.
