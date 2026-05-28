---
name: dashboards
description: >
  Build and open the agent's live, always-fresh CRM dashboards inside Cowork. Use when the agent says
  "open my dashboard", "show my command center", "show my pipeline", "show my hot leads", "commission
  forecast", "build my dashboard", or "open my [X] dashboard". Generates a self-contained live artifact
  wired to the CRM that re-queries fresh data every time it's opened — read-only, no re-prompting.
metadata:
  version: "1.0.0"
---

# Dashboards

Turn the agent's CRM into living, glanceable dashboards. This skill generates **Cowork live
artifacts** — live dashboards that save in Cowork (in the **Live artifacts** sidebar) and refresh
themselves, pulling **fresh data** from the CRM every time they're opened (no re-prompting, no cost
on refresh), remembering their filters across sessions, and keeping a version history. They are
**read-only** — dashboards never change anything in the CRM.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
REQUIRES the CRM connector (~~crm / Follow Up Boss) connected — live artifacts can only use
connectors approved when they were created. Confirm it's connected before building.

  ✓ Self-contained HTML live artifact, wired to the CRM's read tools
  ✓ Re-queries the CRM on every open (short cache + a manual Refresh button)
  ✓ Persists filters/selections across sessions; keeps version history
  ✓ Read-only: surfaces data, never edits it
```

## Before you start

First call `get_my_profile` on the `~~crm` connector to get the agent's business context — average
sale price, average commission %, market/service area, niche — since for managed clients this is set
server-side and needs no local setup. If `get_my_profile` is unavailable, fall back to reading the
agent's `CLAUDE.md` (saved by the **remember** skill) for the same context plus pipeline stage names
and follow-up cadence. The Commission Forecast in particular needs sale price and commission % —
pull them from `get_my_profile`, then `CLAUDE.md`, or ask once and offer to save them via
**remember**.

If the CRM connector isn't connected, tell the agent: *"Connect your CRM first (Settings →
Connectors) — a live dashboard can only pull data from a CRM that's connected before I build it,"*
then proceed once it's connected.

## The four dashboards

### 1. Daily Command Center (default)

The home base — what matters right now, top to bottom.

- **Shows:** today's appointments, the #1 priority, hot leads to act on, and tasks due/overdue.
- **Feeds:** `fub_list_appointments` (today), `fub_list_tasks` (due + overdue), `fub_list_leads`
  (new + hot).
- **Layout:** header with title, date, and a **Refresh** control → a row of summary cards
  (appointments today, tasks due, hot leads, overdue) → an **Appointments** timeline → a **Hot
  Leads** list → a **Tasks Due** checklist.
- **Persistent filters:** none required; remembers any card the agent collapses/expands.

### 2. Hot Leads

The leads most worth a call right now.

- **Shows:** newest and highest-intent leads, each with last-touch recency and a **suggested next
  action** (call now, reply, send listings).
- **Feeds:** `fub_list_leads` (sorted by score / recency), `fub_get_contact_activity` (last touch
  per lead).
- **Layout:** header + Refresh → filter bar (**stage**, **source**) → a sortable table: Lead ·
  Source · Stage · Last touch · Why now · Suggested action.
- **Persistent filters:** selected stage(s) and source(s), and the active sort.

### 3. Pipeline

The whole book of business, by stage, with what's going stale.

- **Shows:** contacts grouped by stage with a count per stage, plus a **stale flag** on anyone with
  no recent touch (default: 7+ days for active stages — honor any cadence in `CLAUDE.md`).
- **Feeds:** `fub_list_leads` (grouped by stage), `fub_get_contact_activity` (last touch → stale
  detection).
- **Layout:** header + Refresh → a column/kanban-style board (one column per stage with its count)
  → stale contacts visually flagged within each column → a "Going stale" summary card up top.
- **Persistent filter:** **stage** (which stages are shown).

### 4. Commission Forecast

Projected commission by expected close — clearly labeled as an estimate.

- **Shows:** projected commission grouped by expected close window (this month / next / this
  quarter), with a running total.
- **Feeds:** `fub_list_leads` (+ agent-provided assumptions).
- **The estimate:** most CRMs (incl. Follow Up Boss) don't expose deal/price data through these
  tools. When they don't, estimate from **pipeline volume × average sale price × commission %**
  (pull average sale price and commission % from `CLAUDE.md`, or ask once). **Label every output as
  an estimate** — header banner *"Estimates based on your pipeline and assumptions"* and an
  assumptions footnote showing the numbers used. If the CRM ever does expose real deal/price data,
  prefer it and note which figures are actual vs. estimated.
- **Layout:** header + Refresh + an **assumptions** strip (sale price, commission %) → summary cards
  (projected this month / quarter / total) → a table by close window with weighted estimates.
- **Persistent filters:** the assumptions (sale price, commission %) and the selected time horizon.

## How to build one

1. **Pick the dashboard.** Match the agent's words to one of the four; if it's ambiguous or they
   just say "open my dashboard", **default to the Daily Command Center**.
2. **Confirm the CRM connector.** A live artifact can only use connectors approved at build time, so
   verify `~~crm` is connected first (see *Before you start*). For Commission Forecast, also confirm
   the sale-price and commission-% assumptions.
3. **Pull the data** with the read tools for that dashboard (listed above) so you have a real,
   current snapshot to design and validate the layout against. Read-only — never write.
4. **Generate the live artifact.** Produce a **self-contained HTML** dashboard (inline CSS/JS, no
   external dependencies) with a clean, modern, real-estate-appropriate aesthetic: a header bar with
   the title and a **Refresh** button, summary **cards**, and **tables/lists** for detail. Wire the
   data layer to the CRM tools so the artifact **re-queries on open** (and on Refresh), rather than
   baking in a static snapshot.
5. **Save it as a live artifact** so it lands in Cowork's **Live artifacts** sidebar with version
   history.
6. **Persist state.** Use the live-artifact storage to remember the dashboard's filters/selections
   (stage, source, assumptions, sort) across sessions.
7. **Hand off.** Tell the agent it's saved in the **Live artifacts** sidebar, refreshes
   automatically (and there's a **Refresh** button), and is free to re-open anytime.

## Aesthetic & build notes

- **Clean and professional:** generous whitespace, a restrained palette, one accent color, legible
  type, clear card/section hierarchy. Avoid clutter — this is a tool the agent opens daily.
- **Fast to read:** lead with summary cards (counts/totals), then detail tables. Color-code lightly
  (e.g. overdue/stale in a warning tone) — never rely on color alone.
- **Responsive:** usable on a laptop or a phone between showings.
- **Self-contained:** everything inline; the only live dependency is the CRM connector.

## Refresh, persistence & state

- **Fresh on open:** the artifact re-queries the `~~crm` each time it's opened, using a short cache;
  the **Refresh** button forces an immediate re-pull. No prompt and no token cost to refresh.
- **State persists:** filters, sorts, and the Commission Forecast assumptions are saved in the
  live-artifact storage and restored next session.
- **Version history:** Cowork keeps prior versions; rebuilding to add a section or fix layout
  creates a new version, not a duplicate.
- **Connector lock:** the artifact can only use connectors approved when it was created or last
  updated. If the agent adds a connector later, **rebuild/update** the dashboard so it can use it.

## Write safety

These dashboards are **read-only** — they only call CRM read tools (`fub_list_leads`,
`fub_list_appointments`, `fub_list_tasks`, `fub_get_contact_activity`, `fub_search_contacts`,
`fub_get_contact`) and never write. To act on what a dashboard surfaces (log a note, set a task,
draft a reply), hand off to a skill that writes with confirmation.

## Related skills

- **daily-briefing** — the one-time, written morning brief; the Daily Command Center is its
  persistent, always-on counterpart.
- **follow-up** — act on the stale and hot contacts a dashboard surfaces (drafts + CRM writes,
  always confirmed).
- Reads context saved by **remember** (`CLAUDE.md`) — average sale price, commission %, stage names,
  cadence; writes nothing.
