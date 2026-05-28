# Connectors — Realtor OS Listings & Market Intel

This package is **web-search only** — no MLS, no paid data API, no scraping pipeline. Skills use
Claude's built-in web search to research properties, comps, and markets, with strict guardrails so
nothing inaccurate reaches a client.

## What it uses

- **Web search (built-in):** the data source for comps, listings, market stats, and neighborhood info.
- **`~~crm` (from Core, optional):** `buyer-match` reads a buyer's saved criteria/contacts via the CRM connector; `get_my_profile` supplies the agent's market/areas for context. If Core's CRM connector isn't connected, the agent provides the criteria directly.

## Accuracy guardrails (apply to every skill here)

1. **No uncited numbers** — every price, sqft, bed/bath, or date carries a source URL + date; if not found, say "not found" (never estimate silently).
2. **Label the number type** — List vs Sold vs Estimate, explicitly.
3. **Confidence + recency tag** on each data point.
4. **Public/authoritative sources first** (county records, reputable news) over restricted portals.
5. **Completeness caveat** — "this is what I could find online, not a comprehensive comp set."
6. **Verify-before-client disclaimer** on anything that drives pricing or advice.
7. **Human in the loop** — present as a draft for the agent to confirm.
8. **Timestamp** the research.

Outputs are **research drafts to verify against the MLS**, not authoritative CMAs.
