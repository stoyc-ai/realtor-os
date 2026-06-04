# Connectors — Realtor OS Core

## How tool references work

Skills in this plugin refer to tools by **category** using a `~~` placeholder, not by product name. The most important one:

- `~~crm` → the agent's real estate CRM (Follow Up Boss by default; also kvCORE, Sierra, etc.)

This keeps the plugin **CRM-agnostic**: the same skills work for any agent once their CRM is connected.

## The CRM connector (`~~crm`) — added per client

There is **no native Follow Up Boss connector**, so the CRM is connected as a **custom remote MCP connector** that STOYC hosts (the agent's API key lives on STOYC's server, never in Cowork).

**Setup (done once, during onboarding):**
1. STOYC stores the agent's CRM API key server-side and provides a unique URL: `https://<stoyc-host>/c/<token>/mcp`
2. In Cowork → **Settings → Connectors → Add custom connector**:
   - **Name:** `Follow Up Boss`
   - **Remote MCP server URL:** the URL STOYC provided
   - Leave OAuth fields blank → **Add**

Once connected, these tools are available to the skills:
`fub_search_contacts`, `fub_get_contact`, `fub_list_leads`, `fub_get_contact_activity`, `fub_list_tasks`, `fub_list_appointments`, `get_my_profile` (read — `get_my_profile` returns the agent's brand voice + business info: brokerage, service areas, niche, and operating rules) and `fub_add_note`, `fub_update_lead`, `fub_create_task`, `fub_create_lead`, `fub_log_call` (write — skills always confirm before writing).

**Server-hosted voice + business profile.** An agent's brand voice and business profile can be served straight from the connector via `get_my_profile`, set server-side by STOYC during onboarding. This means a client's voice (tone, conventions) and business facts (brokerage, areas, niche, average commission %, rules) can be baked into their connector with **no files on the client's machine** — content sounds like them and skills get business context without the agent setting anything up locally.

## Native connectors (the agent self-authorizes)

| Category | Connector | Used by |
|----------|-----------|---------|
| Email | Gmail | lead-reply, follow-up, daily-briefing |
| Calendar | Google Calendar | daily-briefing, appointment-prep |
| Files | Google Drive | learn-my-voice (pull writing samples), appointment-prep |

Connect these in Cowork → Settings → Connectors. Skills **degrade gracefully**: if a connector isn't present, the skill asks the agent to paste the info instead.

## Working folder

`learn-my-voice` and `remember` save files (`voice-profile.md`, `CLAUDE.md`, `memory/`) to the agent's **selected working folder**. Pick a working folder in Cowork once during onboarding so these persist across sessions.
