---
name: single-property-page
description: >
  Write full single-property landing page / listing website copy in the agent's voice for a specific
  address. Use when the agent says "property landing page", "single property site for [address]",
  "listing website copy", "landing page for my new listing", or "write the website for [address]".
  Returns all page sections — hero headline, description, feature highlights, neighborhood blurb, CTA,
  FAQ — plus a JSON-LD schema suggestion, ready to paste into Webflow or WordPress. Fair Housing
  critical. Drafts only.
metadata:
  version: "1.0.0"
---

# Single-Property Landing Page

Write the complete copy for a single-listing website — the standalone page agents put up for a new
listing — in *the agent's* voice, written to win over buyers and to show up in Google and AI search.
Output is split into sections and ready to paste into Webflow or WordPress, with a schema suggestion
for the web person. This is a **draft-only** skill, and **Fair Housing is critical** here: a public
listing page is exactly where the wrong wording gets agents in trouble.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent gives the address + property facts (beds/baths/sqft/price/features)
  ✓ Loads voice + area via get_my_profile (fallback voice-profile.md / CLAUDE.md)
  ✓ Output: hero → description → feature highlights → neighborhood → CTA → FAQ + schema note
SUPERCHARGED (when connected)
  + Webflow / WordPress: format per CMS (note where each section + schema go)
  + web search: objective neighborhood facts (amenities, named places, commute) — cited
```

## Load first (every time)

1. **Load the agent's voice + market.** Call `get_my_profile` first — brand voice (tone, examples,
   sign-off) + business info (brokerage, service areas, name, license #). Write in their voice. If
   unavailable / "No profile configured," fall back to `voice-profile.md` and `CLAUDE.md`; if neither
   exists, ask.
2. **Gather the property facts.** Address, price, beds/baths, square footage, lot, year built, standout
   features, upgrades, included items, showing/open-house info, the listing agent's contact. Never
   invent specs — if a detail is unknown, leave `[fill in: …]`.

## Fair Housing guardrail (critical — this is a public page)

Describe **the home and its features**, and the **neighborhood by objective facts** — never the
**people** who do or "should" live there. Hard rules:

- **No targeting / preference / limitation** by race, color, religion, national origin, sex, familial
  status, or disability — directly or by implication.
- **Banned phrasings:** "perfect for families," "great for a young couple," "safe neighborhood,"
  "good schools for kids like yours," "exclusive," "ideal for empty nesters," "walking distance to
  church/temple," anything implying who belongs there.
- **Do this instead:** describe rooms, finishes, layout, lot, systems, and **objective** nearby
  amenities ("0.4 mi to Lincoln Park," "8-minute drive to downtown," "in the Whitman school
  attendance zone" — a factual boundary, not a quality judgment).
- Accessibility features may be **described as features** ("single-level, no-step entry, 36" doorways")
  — that's stating facts about the home, not targeting a protected class.

## Output format

```markdown
# Listing Page — [Address]
**Voice:** [source] · **For:** [Webflow / WordPress / paste-ready]
**Facts used:** [price · beds/baths · sqft · key features — or "as provided"]

---

## SEO meta
- **Title tag (≤60):** [Address] | [beds/baths] in [Area]
- **Meta description (≤155):** [hook + key features + CTA]
- **URL slug:** /[address-kebab]

## Hero
**Headline:** [short, evocative, feature-led — no people targeting]
**Subhead:** [price · beds/baths · sqft · 1-line hook]
**Primary CTA button:** [e.g. "Book a private showing"]

## Property description
[2–3 paragraphs in their voice. Walk the buyer through the home — layout, light, finishes, flow,
upgrades. Concrete and sensory, about the property only.]

## Feature highlights
- [Feature — specific, e.g. "Renovated chef's kitchen, quartz counters (2023)"]
- [Feature]
- [Feature]
- [Feature]  *(4–8 scannable bullets)*

## The neighborhood
[Short blurb — objective facts only: named parks, distances, transit, attractions, school
attendance zone as a boundary fact. *[source, date]* for any stat. No quality/people judgments.]

## Call to action
[1–2 lines + clear next step: showing, open house [date/time], or "request details".]
**Contact:** [agent name], [brokerage] · [phone/email] · License #[…]

## FAQ
**Is this home still available?** [Answer / "Yes as of [date]".]
**What's included in the sale?** [Answer.]
**How do I schedule a showing?** [Answer with the CTA.]
**[Property-specific Q — e.g. HOA / parking / lot]?** [Answer.]

---

## Suggested schema — for your web person (paste into the page head / CMS custom code)
> Schema = a snippet of code that tells Google/AI what's on the page (here: that it's a home for
> sale). Hand this to whoever manages your site; you don't need to touch it.
\`\`\`json
{
  "@context": "https://schema.org",
  "@type": "SingleFamilyResidence",
  "name": "[Address]",
  "address": { "@type": "PostalAddress", "streetAddress": "[…]", "addressLocality": "[…]",
    "addressRegion": "[…]", "postalCode": "[…]" },
  "numberOfRooms": "[…]", "floorSize": { "@type": "QuantitativeValue", "value": "[sqft]", "unitCode": "FTK" }
}
\`\`\`
> Pair with a `RealEstateListing`/`Offer` (price) block and a `FAQPage` block (the FAQ above). List
> agent as `Person` + brokerage as `Organization` to reinforce E-E-A-T.

**Paste guide:** Title + meta in the [Webflow/WordPress] page SEO settings; each `##` is a section/
block; JSON-LD in the page's custom code/head.
```

If Webflow/WordPress is connected, format per CMS and note placement. If not, output paste-ready.

## Execution flow

1. **Parse the request** — get the address + any property facts provided.
2. **Load voice + business** — `get_my_profile`; fall back to `voice-profile.md` / `CLAUDE.md`.
3. **Gather facts** — confirm specs; mark unknowns `[fill in: …]`. Optional web search for **objective**
   neighborhood facts, cited.
4. **Draft the page** — hero, description, feature highlights, neighborhood blurb, CTA, FAQ — in their
   voice, plus SEO meta and a schema suggestion.
5. **Fair Housing pass** — scan every line; strip any people-targeting or steering language before output.
6. **Format for the CMS** — Webflow/WordPress placement notes (or paste-ready).
7. **Output the draft** using the template. Do **not** publish.
8. **Offer next steps** — e.g. "Want the matching just-listed social posts (**just-listed-sold**) or
   an email blast to your list?"

## Write safety

- **Drafts only** — never publishes the page.
- Never invent specs, price, or availability — unknowns become `[fill in: …]`.
- The Fair Housing pass is mandatory; when in doubt, describe the home, not the buyer.

## Related skills

- **just-listed-sold** — social posts announcing the same listing.
- **blog-seo** — neighborhood / market articles that link to listing pages.
- **seo-audit** — checks the site (incl. listing pages) for SEO + schema.
- **newsletter** — feature the listing in the monthly email.
- **learn-my-voice** — builds the `voice-profile.md` this skill writes from.
