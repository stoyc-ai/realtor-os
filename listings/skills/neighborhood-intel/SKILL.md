---
name: neighborhood-intel
description: >
  Build a factual neighborhood profile — lifestyle, schools, amenities, transit, dining, and recent
  news/development — from live web research. Use when the agent says "neighborhood intel for [area]",
  "what's [area] like", "schools and amenities in [area]", "tell me about living in [area]", or
  "what's happening in [area]". Researches public sources, cites every claim, and returns an
  area profile the agent can use with clients — describing places and features, never the people.
metadata:
  version: "1.0.0"
---

# Neighborhood Intel

Give any area a rich, factual profile: what daily life and amenities are like, the assigned schools,
transit and commute, dining and recreation, and what's recently changed or is being built. This
plays to web search's strength — qualitative, public context — rather than precise pricing. It is
**web-search only** and every claim is cited; treat it as a **research draft to verify** before it
informs client advice.

## How it works

```
ALWAYS (works standalone)
  ✓ Agent names the area (neighborhood, ZIP, town, or district)
  ✓ Web search: lifestyle, schools, amenities, transit, dining, recreation, recent news/development
  ✓ Output: a neighborhood profile — factual, cited, recency-tagged, no demographic/steering language
SUPERCHARGED (when connected)
  + get_my_profile (~~crm): the agent's primary area — used as the DEFAULT if none is given
```

## Inputs to gather

- **Area** — neighborhood, ZIP, town, or district. If unspecified, **default to the agent's primary
  area from `get_my_profile`** and confirm.
- **For whom (optional)** — a relocating buyer, a family, a downsizer, an investor — so emphasis fits
  (schools vs walkability vs amenities). This shapes which factual features to surface, never any
  characterization of people.

## Accuracy guardrails (mandatory — from CONNECTORS.md)

- **No uncited claims or numbers.** Every school rating, commute time, amenity, and date carries a
  source URL + the date published or pulled. If not found, write "not found" — never invent it.
- **Label any figure's type** — e.g. a school rating's scale and source, an Estimate vs a reported
  figure. Don't present an aggregator opinion as fact.
- **Confidence + recency tag** per item — High/Medium/Low and how fresh. Flag stale news.
- **Public/authoritative sources first** — school district sites, municipal/transit pages, and
  reputable local news over review-aggregator portals.
- **Completeness caveat** — this is what's publicly discoverable, not exhaustive; the agent's local
  knowledge fills gaps.
- **Verify-before-client disclaimer** — confirm anything that could drive a decision (school
  assignment, a development claim, safety/hazard info) before advising a client; boundaries and
  assignments change.
- **Human in the loop** — present as a draft for the agent to confirm.
- **Timestamp** the research.
- **Fair Housing (critical for this skill).** Describe **places, features, and amenities factually**.
  Do **not** characterize the people who live there, reference race/religion/national origin/familial
  status/disability or any protected class, use coded terms ("good/bad area," "family-friendly" as a
  proxy, "safe" as a demographic signal), or steer a client toward/away from an area based on who
  lives there. Report objective, cited facts (e.g. a published school rating, a transit line, a park)
  and let the client decide.

## Output format

```markdown
# Neighborhood Profile (Web Research): [Area]
*Researched [date/time]. Web-search draft — verify decision-driving items (school assignment,
development, hazards) before advising a client. Factual area features only.*

## Overview
[2–3 sentences: what kind of area it is in factual terms — housing stock, setting, character of the
built environment — sourced and recency-tagged. No demographic language.]

## Lifestyle & Amenities
- **Daily amenities:** [grocery, retail, services nearby] *(source, date)*
- **Recreation/parks:** [parks, trails, recreation] *(source, date)*
- **Dining:** [notable spots / districts] *(source, date)*

## Schools
*(Factual; cite the rating source + scale. Note assignments can change — verify.)*
| School | Level | Rating (source/scale) | Conf. | Source (date) |
|--------|-------|----------------------|-------|---------------|
| [name] | Elem. | [X/10, source] | [H/M/L] | [url] *(date)* |

## Getting Around
- **Transit:** [lines/stations, factual] *(source, date)*
- **Commute anchors:** [drive/transit time to a named hub, if cited] *(source, date)*
- **Walk/bike:** [factual infrastructure / cited score] *(source, date)*

## Recent News & Development
- [Permits, new construction, business openings/closings, infrastructure] *(source, date)*

## What I could not confirm
- [Each "not found" / stale item and where to verify — district site, municipal records, on-site]

---
*Draft for your review. Tell me to focus on schools, transit, or development, or to re-pull stale news.*
```

## Execution flow

1. **Resolve the area.** Use the stated area; if none, default to the agent's primary area from
   `get_my_profile` (fall back to `CLAUDE.md`) and confirm. Read-only.
2. **Research lifestyle & amenities (web).** Find grocery/retail/services, parks and recreation, and
   dining. Cite each, tag confidence + recency. Keep it to places and features.
3. **Research schools (web).** Pull assigned schools and any published ratings, citing the source and
   scale. Note that assignments and boundaries change and must be verified — never imply a school is
   a proxy for "who lives there."
4. **Research getting around (web).** Transit lines/stations, commute times to named hubs (only if
   cited), and walk/bike infrastructure or cited scores.
5. **Scan recent news & development (web).** Permits, new construction, business changes, and
   infrastructure projects. Cite and date each; flag stale items.
6. **Apply the Fair Housing filter.** Before writing, strip any language that characterizes people,
   references a protected class, uses coded terms, or steers. Keep only objective, cited facts.
7. **Assemble the profile** in the template; put unconfirmed or stale items in "What I could not
   confirm" with where to verify them.
8. **Deliver as a draft.** Timestamp it, present for the agent's review, and offer to focus a section
   or re-pull stale news. Write nothing back to the CRM.

## Related skills

- **market-report** — the pricing/inventory trend for the same area.
- **property-research** — a specific address within the neighborhood.
- **buyer-match** — listings that fit a buyer within these areas.
- **appointment-prep** (Core) — bring this profile into a buyer consult or open house.
