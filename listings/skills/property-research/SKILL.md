---
name: property-research
description: >
  Deep-research a specific property and its surrounding neighborhood from the open web. Use when the
  agent says "research [address]", "tell me about [property]", "property history for [address]",
  "dig into [address]", "what's the story on [address]", or "background on this listing". Pulls
  public records, listing/price history, neighborhood context, schools, and recent news — every
  figure cited, dated, and labeled — into a property dossier for the agent to verify.
metadata:
  version: "1.0.0"
---

# Property Research

Build a complete dossier on a single property: what the public record says, how it has listed and
sold over time, what the surrounding neighborhood and schools are like, and any recent news that
touches it. This skill is **web-search only** — county records and reputable sources, not an MLS
feed — so figures are **research to verify** before they inform pricing or reach a client. The agent
provides the address; the profile and CRM only add context.

## How it works

```
ALWAYS (works standalone)
  ✓ Agent gives the property address
  ✓ Web search: public/assessor records, listing & price history, neighborhood, schools, recent news
  ✓ Output: a property dossier — facts, history, context — every figure cited, dated, labeled
SUPERCHARGED (when connected)
  + get_my_profile (~~crm): the agent's market/area for local context
  + ~~crm contact: if the property ties to a known buyer/seller, pull their notes and timeline
```

## Inputs to gather

- **Address** — full street address (required).
- **Why** — listing prep, buyer due-diligence, a neighbor's "what did it sell for" question, or
  general background. Shapes emphasis, not the facts.
- **Known details (optional)** — anything the agent already knows first-hand (condition, updates,
  off-market history) — this beats anything online; capture and label it as agent-provided.

## Accuracy guardrails (mandatory — from CONNECTORS.md)

- **No uncited numbers.** Every price, sqft, bed/bath, lot size, year built, tax/assessment figure,
  and date carries a source URL + the date it was published or pulled. If not found, write "not
  found" — never estimate silently.
- **Label the number type** — **List**, **Sold**, **Estimate** (Zestimate/aggregator), or
  **Assessed** (county). Never blend a sold price with an estimate.
- **Confidence + recency tag** per data point — High/Medium/Low and how fresh the source is. Flag
  stale listing data.
- **Public/authoritative sources first** — county assessor/recorder, tax records, and reputable news
  over restricted aggregator portals; mark aggregator-only figures as estimates.
- **Completeness caveat** — this is what's publicly discoverable, not a full MLS/title history;
  off-market events and recent activity may not appear.
- **Verify-before-client disclaimer** — confirm any figure (especially price/sale history) in the
  MLS, county records, or title before it drives pricing, advice, or a client conversation.
- **Human in the loop** — present as a draft for the agent to confirm.
- **Timestamp** the research.
- **Fair Housing** — describe the **property, the housing stock, and factual area features**. Never
  characterize the people in the neighborhood, reference demographics or protected classes, or use
  steering language. Report school data factually (e.g. a cited rating), never as a proxy for
  desirability of "who lives there."

## Output format

```markdown
# Property Dossier (Web Research): [Address]
*Researched [date/time]. Web-search draft — verify figures in MLS / county records before client use.*

## Snapshot
- **Address:** [full address]
- **Type:** [single-family / condo / etc.] *(source, date)* or "not found"
- **Beds/baths · sqft · lot · year built:** [values, each cited] *(source, date)* or "not found"
- **Current status:** [Active / Pending / Off-market / Sold] *(source, date)*

## Public Record
| Item | Value | Type | Conf. | Source (date) |
|------|-------|------|-------|---------------|
| Owner of record | [name or "not found"] | — | [H/M/L] | [url] *(date)* |
| Assessed value | $[X] | Assessed | [H/M/L] | [url] *(date)* |
| Annual taxes | $[X] | — | [H/M/L] | [url] *(date)* |
| Last recorded sale | $[X], [date] | Sold | [H/M/L] | [url] *(date)* |

## Listing & Price History
| Date | Event | Price | Type | Source (date) |
|------|-------|-------|------|---------------|
| [date] | Listed | $[X] | List | [url] *(date)* |
| [date] | Sold | $[X] | Sold | [url] *(date)* |
| [date] | Est. value | $[X] | Estimate | [url] *(date)* |

## Neighborhood & Schools
*(Factual, cited. No demographic or steering language.)*
- **Location/context:** [factual area description] *(source, date)*
- **Schools:** [assigned schools + any cited rating] *(source, date)*
- **Amenities/transit:** [factual nearby features] *(source, date)*

## Recent News / Development
- [Any cited item touching the property or block — permits, development, hazards] *(source, date)*

## What I could not confirm
- [Each "not found" / unverified item and where to verify — MLS, county records, title, on-site]

---
*Draft for your review. Tell me to go deeper on history, schools, or news, or to re-pull anything stale.*
```

## Execution flow

1. **Load context.** Call `get_my_profile` for the agent's market (fall back to `CLAUDE.md`); if the
   property ties to a known contact, pull their notes. Read-only.
2. **Confirm the address** and capture any agent-provided first-hand details, labeled as such.
3. **Pull public records (web).** Search county assessor/recorder and tax records for owner of
   record, assessed value, taxes, and last recorded sale. Cite each, label the type, tag confidence
   + recency. Write "not found" where unavailable.
4. **Reconstruct listing & price history (web).** Find prior list/sold events and any current
   estimate. Label each List/Sold/Estimate with its date and source; flag stale or aggregator-only
   figures.
5. **Research neighborhood & schools (web).** Pull factual area context, assigned schools (with any
   cited rating), amenities, and transit — public sources first. Keep it to home/area features; no
   demographics, no steering.
6. **Scan recent news/development (web).** Look for permits, nearby development, hazards, or news
   touching the property or block. Cite and date each item.
7. **Assemble the dossier** in the template. Put every unverified or missing item in "What I could
   not confirm" with where to verify it.
8. **Deliver as a draft.** Timestamp it, present for review, and offer to deepen any section or
   re-pull stale data. Write nothing back to the CRM.

## Related skills

- **cma-builder** — price this property against comps.
- **neighborhood-intel** — a fuller lifestyle/schools/amenities profile of the area.
- **market-report** — the market trend the property sits inside.
- **appointment-prep** (Core) — fold this dossier into a showing or listing brief.
