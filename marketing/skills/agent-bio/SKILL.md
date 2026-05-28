---
name: agent-bio
description: >
  Write the agent's bio / About page / personal-brand copy in their own voice, from their real story,
  credentials, areas, and niche. Use when the agent says "write my bio", "about page", "agent bio",
  "my About section", "bio for Zillow/Realtor.com", or "personal brand copy". Returns short (1–2
  sentence), medium, and long bios plus an About-page version optimized for E-E-A-T and entity
  clarity — tying into seo-audit. Drafts only.
metadata:
  version: "1.0.0"
---

# Agent Bio / About Page

Write the agent's bio in *their* voice and from *their* real story — the short blurb for socials, the
medium version for a directory profile, the long version, and a full About-page version built to win
trust with both people and search engines. The About-page version is tuned for **E-E-A-T**
(Experience, Expertise, Authoritativeness, Trust) and **entity clarity** — exactly what **seo-audit**
flags on weak About pages. Drafts only.

## How it works

```
ALWAYS (works standalone)
  ✓ Agent says "write my bio" / "about page"
  ✓ Loads their story + credentials + areas + niche via get_my_profile (fallback files)
  ✓ Output: short + medium + long bios + an E-E-A-T-optimized About-page version
SUPERCHARGED (when connected)
  + Webflow / WordPress: format the About-page version + a Person schema note for the CMS
  + web search: verify a named credential/award/affiliation phrasing (don't invent any)
```

## Load first (every time) — lean on the profile heavily

1. **Load the agent's voice + full story.** Call `get_my_profile` first — brand voice (tone, examples,
   sign-off) + business info: name, **brokerage, service areas, target neighborhoods, niche**, years in
   business, credentials/designations, license #, awards, languages, what makes them different. This
   skill draws on **more** of the profile than any other — the bio *is* their story. If unavailable /
   "No profile configured," fall back to `voice-profile.md` and `CLAUDE.md`; if details are thin, ask
   a few targeted questions (years, niche, areas, one proof point) rather than inventing.

**Never fabricate** credentials, sales numbers, awards, or years of experience. If a proof point isn't
in the profile and the agent doesn't give it, leave `[fill in: …]` — invented credentials are both a
trust and a compliance problem.

## E-E-A-T & entity clarity (for the About-page version — from seo-audit)

- **Entity clarity** — state plainly and consistently who they are, where they serve, and what they
  specialize in: "[Name] is a [brokerage] agent specializing in [niche] in [neighborhoods], [city]."
  This exact-clarity sentence is what AI engines cite.
- **Experience** — concrete tenure + lived local knowledge ("Lived in [area] for 12 years; closed
  [X] homes there").
- **Expertise** — named credentials, designations (ABR, CRS, SRS…), niche focus.
- **Authoritativeness** — brokerage, affiliations, press, awards (only real ones).
- **Trust** — license #, real contact, testimonials nearby, no inflated claims.
- Use the agent's name + brokerage consistently (same NAP/entity as GBP/Zillow) to reinforce the
  entity across the web.

## Fair Housing guardrail

Describe **the agent and their service** — never imply they serve or prefer a protected class. Avoid
"I help families find…", "great for young professionals," or any phrasing tying their service to
race, color, religion, national origin, sex, familial status, or disability. "I specialize in
first-time buyers" / "luxury condos" / "[neighborhood] homes" describes a **service niche** and is
fine; "I help [protected group]" is not. Languages spoken may be stated as a factual service feature.

## Output format

```markdown
# Bios — [Agent Name]
**Voice:** [source] · **Niche:** [niche] · **Areas:** [areas] · **For:** [Webflow / WordPress / paste]

---

## Short bio (1–2 sentences · social, signatures, IG)
[Punchy, voice-forward. Name + brokerage + niche + area.]

## Medium bio (~50–80 words · Zillow/Realtor.com, directory profiles)
[A tighter narrative: who they are, niche, area, one proof point, a human note, a soft CTA.]

## Long bio (~150–250 words · website, listing presentations)
[Full narrative in their voice: their path into real estate, expertise + credentials, the areas and
niche they own, how they work / what clients get, and a warm close + CTA. Real proof points only.]

---

## About-page version (E-E-A-T + entity optimized)
**Entity sentence (lead with it):** [Name] is a [brokerage] real estate agent specializing in [niche]
in [neighborhoods], [city/region].

[Opening — restate the entity sentence in their voice; establish experience.]

### Experience & local expertise
[Tenure, lived local knowledge, named neighborhoods, track record — concrete + cited where it's a
public fact.]

### Credentials & specialties
- [Designation / credential] — *[real only]*
- [Niche / specialty]
- [Languages, affiliations]
- License #[…] · [Brokerage]

### How [Name] works with clients
[Their approach / value — in voice.]

[Close + clear CTA: book a call / "let's talk about your move".]

---

## Suggested JSON-LD schema (About page head / CMS custom code)
\`\`\`json
{
  "@context": "https://schema.org",
  "@type": "RealEstateAgent",
  "name": "[Name]",
  "jobTitle": "Real Estate Agent",
  "worksFor": { "@type": "Organization", "name": "[Brokerage]" },
  "areaServed": ["[Area 1]", "[Area 2]"],
  "knowsAbout": ["[niche]", "[neighborhood]"],
  "url": "[website]"
}
\`\`\`
> Use a `Person` block linked to the brokerage `Organization` to reinforce the entity + E-E-A-T.

**Paste guide:** Short bio for socials/signatures; medium for directory profiles; long for the site;
About-page version + schema for the [Webflow/WordPress] About page.
```

If Webflow/WordPress is connected, format the About-page version + schema for the CMS. If not, paste-ready.

## Execution flow

1. **Parse the request** — which length(s) they need; default to delivering all four.
2. **Load the profile heavily** — `get_my_profile` for story, credentials, areas, niche; fall back to
   `voice-profile.md` / `CLAUDE.md`; ask for missing proof points rather than inventing.
3. **Draft all versions** — short, medium, long, and the E-E-A-T About-page version — in their voice.
4. **Apply E-E-A-T + entity clarity** — lead the About page with the clean entity sentence; weave in
   real experience, credentials, license #, and consistent name/brokerage usage.
5. **Add a `RealEstateAgent`/`Person` schema note** for the About page.
6. **Fair Housing pass** — niche framing only; no implied protected-class targeting.
7. **Output the draft** using the template. Do **not** publish.
8. **Offer next steps** — e.g. "Want me to push this into your About page (**single-property-page** /
   site) or run a **seo-audit** to check the rest of the site?"

## Write safety

- **Drafts only** — never publishes.
- **Never fabricate** credentials, awards, sales numbers, or tenure; unknowns become `[fill in: …]`.
- Keep name + brokerage consistent with their other profiles to reinforce the entity.

## Related skills

- **seo-audit** — flags weak About pages for E-E-A-T/entity; this skill fixes them.
- **blog-seo** — author content that links back to the bio (reinforces authority).
- **single-property-page** — uses the agent contact/credentials block this skill produces.
- **learn-my-voice** — builds the `voice-profile.md` this skill writes from.
- **remember** — saves credentials/areas into `CLAUDE.md` so future bios stay accurate.
