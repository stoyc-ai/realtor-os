---
name: cma-builder
description: >
  Build a comparative market analysis (CMA) for a property from live web research. Use when the
  agent says "build a CMA", "CMA for [address]", "what's this home worth", "pull comps for
  [address]", "run comps on [address]", or "ballpark this listing". Researches comparable sales and
  active listings online, labels every figure List vs Sold vs Estimate with a source and date, and
  returns a suggested price range as a draft to verify against the MLS.
metadata:
  version: "1.0.0"
---

# CMA Builder

Turn an address into a comp-backed comparative market analysis in minutes. This skill researches
recent comparable sales, active competition, and pricing context from the open web, then assembles a
subject summary, a fully-cited comps table, and a suggested price range. It is **web-search only** —
no MLS feed — so the output is a **research draft to verify against the MLS**, never an authoritative
CMA. The agent provides the subject property; the CRM and profile only add context.

## How it works

```
ALWAYS (works standalone)
  ✓ Agent gives the subject address (+ any known details: beds/baths, sqft, condition, lot)
  ✓ Web search: recent comparable sales, active competing listings, price history, market trend
  ✓ Output: subject summary + cited comps table + suggested price RANGE (labeled estimate) + narrative
SUPERCHARGED (when connected)
  + get_my_profile (~~crm): the agent's market/area, niche, and pricing rules for context
  + ~~crm contact: if the CMA is for a known seller, pull their address/timeline/notes
```

## Inputs to gather

The skill needs the subject property pinned down before it can find true comps:

- **Address** — full street address of the subject property (required).
- **Known details** — beds/baths, square footage, lot size, year built, condition/updates, and any
  recent renovation. The agent's first-hand knowledge beats anything online; capture it.
- **Purpose** — listing-appointment pricing, a seller's "what's it worth" question, or a buyer's
  offer prep. This shapes how aggressive the range should read.

If the agent only gives an address, proceed and flag every detail you couldn't confirm as "not
found — confirm in MLS / on-site."

## Accuracy guardrails (mandatory — from CONNECTORS.md)

This is the highest-stakes skill in the package: numbers here drive pricing advice. Apply every
guardrail without exception.

- **No uncited numbers.** Every price, sqft, bed/bath count, lot size, and date carries a source URL
  + the date it was published or pulled. If you cannot find a figure, write "not found" — never
  estimate it silently.
- **Label the number type** on every figure: **List**, **Sold**, or **Estimate** (e.g. a Zestimate
  or county-assessed value). Never blend them.
- **Confidence + recency tag** per data point — High/Medium/Low and how fresh the source is.
- **Public/authoritative sources first** — county assessor/recorder records and reputable news over
  restricted or aggregator portals; note when a figure is only an estimate from an aggregator.
- **Completeness caveat** — state plainly this is the comp set you could find online, not a
  comprehensive MLS pull; real comps may exist that aren't public.
- **Verify-before-client disclaimer** — the range is an estimate to confirm against the MLS before
  it informs any pricing decision or reaches the seller.
- **Human in the loop** — present as a draft for the agent to confirm and adjust.
- **Timestamp** the research at the top of the output.
- **Fair Housing** — describe the homes and their features factually. Never reference the people in
  a neighborhood, demographics, or any protected class, and never steer.

## Output format

```markdown
# CMA Draft (Web Research): [Subject Address]
*Researched [date/time]. Web-search draft — verify all figures against the MLS before pricing.*

## Subject Property
- **Address:** [full address]
- **Details:** [beds/baths · sqft · lot · year built] — *(source, date)* or "not found — confirm"
- **Condition/updates (agent-provided):** [notes]
- **Last sale / price history:** [Sold $X, date] *(source, date)* or "not found"

## Comparable Properties
*Each comp cited + dated. Type labeled List / Sold / Estimate. Distance from subject noted.*

| # | Address | Type | Price | Beds/Baths | Sqft | $/Sqft | Dist. | Conf. | Source (date) |
|---|---------|------|-------|-----------|------|--------|-------|-------|---------------|
| 1 | [addr] | Sold | $X *(date sold)* | 3/2 | 1,800 | $X | 0.3 mi | High | [url] *(pulled date)* |
| 2 | [addr] | List | $X (active) | 4/2 | 2,100 | $X | 0.5 mi | Med | [url] *(pulled date)* |
| 3 | [addr] | Estimate | $X (aggregator) | 3/2 | 1,750 | $X | 0.4 mi | Low | [url] *(pulled date)* |

**Adjustments to consider (agent to apply):** [e.g. comp 2 is renovated; subject lot is larger] —
these are judgment calls for the agent, not baked into the numbers above.

## Suggested Price Range (ESTIMATE — verify in MLS)
**$[low] – $[high]**
*This is a web-research estimate derived from the comps above, not an MLS-verified CMA. Confidence:
[High/Med/Low] based on comp quality, recency, and how closely they match the subject. Confirm
against full MLS comps before sharing with the seller or setting a list price.*

## Narrative
[2–4 sentences: where the subject sits relative to comps, what's driving the range, what's competing
right now, and the biggest unknowns that could move the number once verified.]

## What I could not confirm
- [Each missing/estimated figure and where the agent should verify it — MLS, county records, on-site]

---
*Draft for your review. Tell me to widen/narrow the radius, add more comps, exclude a comp, or
re-pull if anything looks stale.*
```

## Execution flow

1. **Load context.** Call `get_my_profile` (on `~~crm`) for the agent's market and any pricing rules;
   if unavailable or "No profile configured," fall back to `CLAUDE.md`. If the CMA ties to a known
   seller contact, pull their property/timeline notes. This step is read-only.
2. **Pin the subject.** Confirm the address and capture the agent's known details and condition
   notes. Mark anything unknown as "not found — confirm."
3. **Research the subject (web).** Look up the subject's public record and any price/listing history.
   Cite each figure, label it List/Sold/Estimate, tag confidence + recency. Write "not found" for
   anything you can't verify.
4. **Find comps (web).** Search for recent comparable **sales** first (best signal), then active
   **listings** (competition), then aggregator **estimates** (lowest weight) — within a tight radius
   and similar beds/baths/sqft/age. Aim for 3–6 usable comps. Prefer county records and reputable
   sources; flag aggregator-only figures as Low confidence.
5. **Build the comps table.** One row per comp with every figure cited, dated, type-labeled, and
   confidence-tagged. Note distance from subject and any obvious difference the agent should adjust
   for — without silently baking adjustments into the numbers.
6. **Derive the range.** Suggest a price range from the comp set, **clearly labeled an estimate to
   verify against the MLS**. State the confidence and what drives it. Never present a single "magic
   number" as fact.
7. **Write the narrative + gaps.** Explain where the subject sits and list everything you couldn't
   confirm with where to verify it.
8. **Deliver as a draft.** Timestamp it, present for the agent's review, and offer to adjust radius,
   comp selection, or re-pull stale data. Write nothing back to the CRM.

## Related skills

- **property-research** — deeper dossier on the subject property and its neighborhood.
- **market-report** — the broader area trend (inventory, DOM, price direction) the range sits inside.
- **appointment-prep** (Core) — fold this CMA into a listing-appointment brief.
- **buyer-match** — the active-listing competition view, from the buyer's side.
