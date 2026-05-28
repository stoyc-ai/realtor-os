---
name: buyer-match
description: >
  Match active listings to a buyer's criteria from live web research. Use when the agent says "find
  homes for [buyer]", "match listings to [criteria]", "what's on the market for my buyer", "homes
  that fit [buyer]'s search", or "pull listings for [buyer]". Reads the buyer's criteria from the
  CRM contact when connected (or takes them from the agent), web-searches matching active listings,
  and returns a cited, status-labeled matched-listings table with fit notes — verify in the MLS.
metadata:
  version: "1.0.0"
---

# Buyer Match

Take a buyer's criteria and surface active listings that fit, with a clear fit note on each. This
skill reads the buyer's saved criteria from the `~~crm` contact when connected, otherwise takes them
from the agent, then web-searches the open market. It is **web-search only**, so it will not surface
every listing — it's a **research draft to verify against the MLS**, which has the complete, live
inventory.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent gives the buyer's criteria (budget, beds/baths, area, must-haves, timeline)
  ✓ Web search: active listings that match — each cited, dated, status-labeled
  ✓ Output: a matched-listings table + per-listing fit notes + completeness caveat + disclaimer
SUPERCHARGED (when connected)
  + ~~crm contact: pull the buyer's saved criteria, budget, timeline, and notes directly
  + get_my_profile (~~crm): the agent's market/areas for context
```

## Inputs to gather

The match is only as good as the criteria. Pull them from the CRM contact when connected; otherwise
ask the agent for:

- **Budget / price range** (required) — and whether it's firm or stretchable.
- **Location** — neighborhoods, ZIPs, or a commute anchor.
- **Beds/baths + size** — minimums and any hard floors.
- **Property type** — single-family, condo, townhome, etc.
- **Must-haves vs nice-to-haves** — factual home features only (e.g. garage, yard, single-story,
  number of bedrooms). Never criteria tied to a protected class or who lives in an area.
- **Timeline** — how soon they need to move (affects how to weight pending vs active).

## Accuracy guardrails (mandatory — from CONNECTORS.md)

- **No uncited numbers.** Every list price, sqft, bed/bath, and listing date carries a source URL +
  the date pulled. If a detail isn't in the source, write "not found" — never estimate silently.
- **Label the number type** — these are almost all **List** prices; say so. Mark any **Estimate**
  (aggregator value) or **Sold** comparison explicitly.
- **Status label per listing** — **Active / Pending / Contingent / Off-market** — and flag that
  online status lags the MLS and may be stale.
- **Confidence + recency tag** per listing — High/Medium/Low and how fresh the source is.
- **Public/authoritative sources first** over restricted portals; note aggregator-only figures.
- **Completeness caveat (critical here)** — state plainly you will not find every matching listing
  online; new, coming-soon, and pocket listings may be missing. The MLS is the source of truth — the
  agent should run the same search there.
- **Verify-before-client disclaimer** — confirm price, status, and availability in the MLS before
  sending anything to the buyer or booking a showing.
- **Human in the loop** — present as a draft for the agent to confirm and curate.
- **Timestamp** the research.
- **Fair Housing** — match strictly on the **home's features and price**, never on neighborhood
  demographics or any protected class. Do not steer the buyer toward or away from areas based on who
  lives there; present factual options that meet the stated criteria.

## Output format

```markdown
# Buyer Match (Web Research): [Buyer name or "buyer criteria"]
*Researched [date/time]. Web-search draft — not every listing appears online. Verify in the MLS.*

## Criteria Used
- **Budget:** [$X–$Y] · **Area:** [areas] · **Type:** [type]
- **Beds/baths:** [min] · **Size:** [min sqft] · **Must-haves:** [features] · **Timeline:** [when]
- *Source of criteria:* [CRM contact / agent-provided]

## Matched Listings
*Each cited + dated. Price is List unless labeled. Status flagged (may lag the MLS).*

| # | Address | Price | Type | Status | Beds/Baths | Sqft | Conf. | Source (date) |
|---|---------|-------|------|--------|-----------|------|-------|---------------|
| 1 | [addr] | $X | List | Active | 3/2 | 1,800 | High | [url] *(date)* |
| 2 | [addr] | $X | List | Pending | 4/2 | 2,100 | Med | [url] *(date)* |

### Fit notes
- **#1 [addr]** — [why it fits: hits budget + beds; lacks the garage they wanted] *(cited details)*
- **#2 [addr]** — [fits area + size, but pending — confirm if still available] *(cited details)*

## Near-misses (worth a look)
- [Listing just outside a criterion — note which one] *(source, date)*

## Completeness caveat
This is the set I could find on the open web — **not** the full MLS inventory. Coming-soon, brand-new,
and off-market listings may not appear. Run the same search in the MLS for the complete, current set.

## What I could not confirm
- [Any "not found" detail or stale status and where to verify it — MLS]

---
*Draft for your review. Tell me to widen/narrow the criteria, add an area, or re-pull stale listings.*
```

## Execution flow

1. **Load the buyer + context.** If `~~crm` is connected, locate the buyer contact and pull their
   saved criteria, budget, timeline, and notes; call `get_my_profile` for market context. If not
   connected, ask the agent for the criteria. Read-only — never write to the CRM.
2. **Lock the criteria.** Confirm budget, area, beds/baths, size, type, must-haves, and timeline.
   Keep must-haves to factual home features only; strip anything that would steer by protected class.
3. **Search active listings (web).** Find listings matching the criteria. Prefer public/authoritative
   sources; capture price, beds/baths, sqft, and listing date for each. Cite and date everything;
   write "not found" for missing details.
4. **Label each listing.** Mark price as List (or Estimate/Sold if that's what the source gives) and
   status as Active/Pending/Contingent/Off-market, flagging that online status may lag the MLS. Tag
   confidence + recency.
5. **Write fit notes.** For each match, say plainly how it fits the criteria and where it falls
   short — based only on cited, factual home features. Add a short near-misses list if useful.
6. **State the completeness caveat prominently** — the web set is partial; the MLS is authoritative.
7. **Deliver as a draft.** Timestamp it, present for the agent to curate, and offer to widen/narrow
   criteria, add areas, or re-pull stale listings before anything reaches the buyer.

## Related skills

- **property-research** — deep dossier on any listing the buyer likes.
- **cma-builder** — sanity-check a target home's price against comps before an offer.
- **neighborhood-intel** — factual area profile for the neighborhoods in the search.
- **appointment-prep** (Core) — prep a showing for the matched listings.
