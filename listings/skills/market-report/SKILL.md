---
name: market-report
description: >
  Produce a neighborhood or farm-area market report from live web research. Use when the agent says
  "market report for [area]", "how's the market in [area]", "what's the market doing in [area]",
  "give me market stats for [neighborhood]", or "is it a buyer's or seller's market in [area]".
  Researches inventory, median price direction, days-on-market, and trend — every figure cited,
  dated, and labeled — and returns a report draft to verify against the MLS.
metadata:
  version: "1.0.0"
---

# Market Report

Give any neighborhood, ZIP, or farm area a clear read on where the market is heading — inventory,
median price direction, days-on-market, and the overall trend — sourced from the open web with every
number cited. This skill is **web-search only**, so the report is a **research draft to verify
against the MLS**, useful for client conversations, listing presentations, and farming content once
the agent confirms the figures.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
ALWAYS (works standalone)
  ✓ Agent names the area (neighborhood, ZIP, city, or farm area)
  ✓ Web search: active inventory, median/avg price + direction, days-on-market, sale-to-list, trend
  ✓ Output: a market report with cited, dated, type-labeled figures + a plain-English read + caveat
SUPERCHARGED (when connected)
  + get_my_profile (~~crm): the agent's primary market/areas — used as the DEFAULT area if none given
```

## Inputs to gather

- **Area** — neighborhood name, ZIP code, city, or named farm area. If the agent doesn't specify,
  **default to their primary market from `get_my_profile`** and confirm.
- **Segment (optional)** — single-family vs condo, a price band, or a property type, if the agent
  farms a specific slice. Otherwise report the area overall and note it's all-types.
- **Purpose (optional)** — listing-presentation stat, a "should I buy/sell now" client question, or
  a farming/newsletter piece. Shapes emphasis, not the numbers.

## Accuracy guardrails (mandatory — from CONNECTORS.md)

- **No uncited numbers.** Every inventory count, median/average price, days-on-market figure, percent
  change, and date carries a source URL + the date it was published or pulled. If a stat isn't
  available, write "not found" — never estimate it silently.
- **Label the number type** — make clear whether a price is a **median List**, **median Sold**, or
  an **Estimate/index** from an aggregator. Note the period it covers (e.g. April 2026, trailing 90
  days).
- **Confidence + recency tag** per figure — High/Medium/Low, and how current the data is. Market
  stats stale fast; flag anything older than ~60–90 days.
- **Public/authoritative sources first** — local Realtor association/MLS public reports, reputable
  news, and recognized market indices over single-portal aggregator pages.
- **Completeness caveat** — state that this is what's publicly reported, not a live MLS pull; the
  agent's MLS will have the authoritative, current numbers.
- **Verify-before-client disclaimer** — confirm figures in the MLS before quoting them to a client
  or in marketing.
- **Human in the loop** — present as a draft for the agent to confirm.
- **Timestamp** the research.
- **Fair Housing** — describe the **market and the housing stock** factually. Never characterize who
  lives in an area, reference demographics or protected classes, or use steering language.

## Output format

```markdown
# Market Report (Web Research): [Area] — [as of date]
*Researched [date/time]. Web-search draft — verify figures in the MLS before client use.*

## The Read (TL;DR)
**[Buyer's / Seller's / Balanced] market, [strengthening / cooling / steady].**
[1–2 sentences a client would understand — what's happening and what it means for them.]

## Key Stats
| Metric | Figure | Type | Period | Conf. | Source (date) |
|--------|--------|------|--------|-------|---------------|
| Active inventory | [N homes] | List | [period] | [H/M/L] | [url] *(date)* |
| Median price | $[X] | [Sold / List] | [period] | [H/M/L] | [url] *(date)* |
| Price direction | [+/-X% YoY or MoM] | [Sold / List] | [period] | [H/M/L] | [url] *(date)* |
| Days on market | [N days] | — | [period] | [H/M/L] | [url] *(date)* |
| Sale-to-list ratio | [X%] | Sold | [period] | [H/M/L] | [url] *(date)* |
| Months of supply | [X] | — | [period] | [H/M/L] | [url] *(date)* |

*Any metric not publicly available is marked "not found" and should be pulled from the MLS.*

## Trend
[2–4 sentences: direction over recent months, what's driving it (rates, supply, seasonality), and
how this area compares to the broader city/region if a cited source supports it.]

## What this means for...
- **Sellers:** [factual implication — e.g. pricing realistically, expected DOM]
- **Buyers:** [factual implication — e.g. competition level, negotiating room]

## What I could not confirm
- [Each "not found" or stale stat and where to verify it — MLS, association report]

---
*Draft for your review. Tell me to narrow to a ZIP/property type, add a comparison area, or re-pull
if any figure looks stale.*
```

## Execution flow

1. **Resolve the area.** Use the agent's stated area; if none, default to their primary market from
   `get_my_profile` (fall back to `CLAUDE.md`) and confirm. Read-only.
2. **Research the core stats (web).** Search for active inventory, median price + direction,
   days-on-market, sale-to-list ratio, and months of supply for the area and period. Prefer
   association/MLS public reports and recognized indices; cite each figure, label its type, note the
   period, and tag confidence + recency.
3. **Mark gaps.** Any stat you can't source goes in as "not found" — never filled with a guess.
4. **Establish the trend.** Look for the recent direction over several months and the drivers
   (rates, supply, seasonality). Only state comparisons (vs city/region) that a cited source backs.
5. **Write the read.** Lead with a plain-English verdict (buyer's/seller's/balanced, strengthening/
   cooling) that a client could repeat, grounded only in the cited stats.
6. **Translate for sellers and buyers.** Factual implications only — pricing, DOM expectations,
   competition, negotiating room. No steering, no demographics.
7. **Deliver as a draft.** Timestamp it, list what couldn't be confirmed, and offer to narrow the
   segment, add a comparison area, or re-pull stale data. Write nothing back to the CRM.

## Quick stat

When the agent says "quick market read" or "one-line market for [area]", skip the full report and
return a single cited line:

```markdown
**[Area], as of [date]:** [Buyer's/Seller's/Balanced], [trend]. Median [Sold/List] $[X] *(source,
date)*, [N] active, [N] days on market *(source, date)*. *Web draft — verify in MLS.*
```

If a figure isn't sourced, say "not found" rather than fill it in.

## Related skills

- **cma-builder** — price a specific property inside this market.
- **neighborhood-intel** — the lifestyle/schools/amenities layer beneath the numbers.
- **property-research** — drill into a single address within the area.
- **appointment-prep** (Core) — bring these stats into a listing presentation.
