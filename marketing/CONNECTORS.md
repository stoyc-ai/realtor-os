# Connectors — Realtor OS Marketing Engine

Skills refer to tools by category (`~~` placeholder) where the specific product varies; `.mcp.json`
pins the defaults. Most content skills also call **`get_my_profile`** (from the Core CRM connector)
to load the agent's brand voice + business info — so all content sounds like them.

## Profile (voice + business)

Content skills load the agent's voice and business facts the same way Core does: via the `~~crm`
connector's **`get_my_profile`** tool (set server-side by STOYC), falling back to `voice-profile.md` /
`CLAUDE.md` in the working folder. Install/connect Core's CRM connector to get this.

## Native connectors (the agent self-authorizes in Cowork → Settings → Connectors)

| Category | Connector(s) | Used by |
|----------|--------------|---------|
| Design | Canva, Figma | social-repurpose, just-listed-sold, reels-script |
| Email marketing | Mailchimp / Klaviyo | newsletter, nurture-drip |
| Website / CMS | Webflow, WordPress | blog-seo, single-property-page |
| Decks | Gamma | (listing/marketing decks) |
| Email / Calendar | Gmail, Google Calendar | newsletter, content-calendar |
| SEO data | Ahrefs, SimilarWeb | seo-audit (optional — enriches the audit with real metrics) |

> Mailchimp, Webflow, WordPress, and Gamma are native Cowork connectors — connect them in Settings.
> Skills degrade gracefully: if a connector isn't present, they produce the content/file for the
> agent to paste in manually.

## Custom connectors (set up by STOYC)

- **`~~gbp` → Google Business Profile** (custom MCP, future): STOYC is added as a Manager on the
  client's GBP and runs the GBP API. Used by `gbp-manager` and `review-response`. Until connected,
  those skills draft posts/replies for the agent to paste into their GBP dashboard.

## Web search

`seo-audit`, `blog-seo`, and `market-update-post` use Claude's built-in web search (no connector) to
read the agent's live website and research topics. Always cite sources.
