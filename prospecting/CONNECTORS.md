# Connectors — Realtor OS Prospecting & Sphere

Skills refer to tools by category (`~~` placeholder) where the specific product varies; `.mcp.json`
pins the defaults. The sphere skills also call **`get_my_profile`** (from the Core CRM connector) to
load the agent's brand voice + business info — so every outreach note sounds like them.

## Profile (voice + business)

Loaded the same way Core does: via the `~~crm` connector's **`get_my_profile`** tool (set server-side
by STOYC), falling back to `voice-profile.md` / `CLAUDE.md` in the working folder. Install/connect
Core's CRM connector to get this.

## Native connectors (the agent self-authorizes in Cowork → Settings → Connectors)

| Category | Connector(s) | Used by |
|----------|--------------|---------|
| Files / Sheets | Google Drive (Sheets) | sphere-radar — reads the agent's sphere from a named Google Sheet |
| Email | Gmail | sphere-radar — optionally drops drafted notes into the agent's Gmail drafts |
| Calendar | Google Calendar | sphere-radar — optional signal of recent interactions worth a touch |

> Skills degrade gracefully: with no Sheet/CRM connected, the agent can paste a list of people and
> the radar still runs.

## Custom connectors (set up by STOYC)

- **`~~crm` → Follow Up Boss** (custom MCP): when connected, `sphere-radar` can pull the sphere
  (past clients, SOI tags, referral partners) straight from the CRM instead of a Sheet, and loads
  voice/business via `get_my_profile`. Provisioned per client by STOYC.

## Web search

`sphere-radar` uses Claude's built-in web search (no connector) to verify recent news, public
LinkedIn activity, press, and funding/IPO signals. Every hit must carry a dated source link.
