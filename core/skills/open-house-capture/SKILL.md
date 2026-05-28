---
name: open-house-capture
description: >
  Turn a photo of a paper open-house sign-in sheet into CRM leads in under a minute. Use when the
  agent says "open house sign-ins", "process this sign-in sheet", "I have open house leads", "add
  these open house contacts", or uploads a photo/scan/snapshot of a handwritten sign-in sheet. Reads
  the image with native vision, parses each row, gets the agent's review, and only then writes leads.
metadata:
  version: "1.0.0"
---

# Open House Capture

The showcase feature: snap a photo of the paper sign-in sheet from this weekend's open house, and
those handwritten rows become real leads in the agent's CRM — duplicates merged, tagged, and labeled
with their source — in under a minute. The agent reviews and fixes any handwriting misreads before
anything is saved.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## How it works

```
1. Claude reads the uploaded photo of the sign-in sheet directly (no extra scanning tool needed).
2. Parses each row into structured fields: First, Last, Email, Phone, Notes.
3. Renders a numbered review table; flags low-confidence cells (handwriting) with a ⚠.
4. The agent corrects misreads and confirms.
5. ONLY THEN: for each contact, de-dupe against ~~crm, then write the lead.
```

This skill **never writes to `~~crm` until the agent has reviewed the full parsed table, corrected
any handwriting errors, and explicitly confirmed.** No silent imports.

## Before reading (always)

1. Confirm the **property address** and **open house date**. If the agent didn't give them, ask —
   they become the lead **source** and **message** so every contact is traceable to this event.
2. If no image is attached yet, ask the agent to upload a photo or scan of the sign-in sheet.

## Reading the sheet

Use **native vision** to read the uploaded image directly — there is no separate OCR step. For each
row on the sheet, extract:

- **First** / **Last** — split the written name; if only one name is legible, put it in First.
- **Email** — exactly as written (watch for `@gmail`/`@gmal`, `.com`/`.con` slips).
- **Phone** — digits only where possible; keep area code.
- **Notes** — any extra column the sheet had: *working with an agent?*, *buyer/seller?*, *timeframe*,
  *price range*, *neighborhood of interest*.

**Never guess.** When a character is ambiguous (a `1` vs `7`, a smudged email, a half-written name),
keep your best read **but flag the cell** with a `⚠` and a trailing `?` (e.g. `j.smith@gmal.com?`).
Skip rows that are blank or completely illegible — list them as skipped, don't fabricate a contact.

## Output format

First, the review table (one row per sign-in, numbered):

```markdown
## Open House sign-ins — [address], [date]
**Read [N] rows. ⚠ = low-confidence, please verify the handwriting.**

| # | First | Last   | Email                 | Phone          | Notes                          | ⚠ |
|---|-------|--------|-----------------------|----------------|--------------------------------|---|
| 1 | Maria | Lopez  | maria.lopez@gmail.com | (408) 555-0192 | Buyer, ~6 mo, pre-approved     |   |
| 2 | Dan   | Cho    | dcho@gmal.com?        | (408) 555-0177 | Has an agent? — left ambiguous | ⚠ |
| 3 | Priya | —      | (illegible)           | 408-555-0143   | Seller, wants a CMA            | ⚠ |

**Skipped:** row 4 (blank), row 7 (illegible).

Fix anything that's wrong (especially ⚠ rows), then reply **"confirm"** and I'll add these to
~~crm — source "Open House", tagged "Open House", with this property + date on each.
```

Then, **after the writes complete**, the summary:

```markdown
## Done — [address], [date]
- ✅ Created: [N] new leads
- 🔀 Merged into existing contacts: [M] (already in your CRM)
- ⏭️ Skipped: [K] (blank/illegible)

[If any duplicates: name them so the agent knows the event was just appended to the existing record.]
```

## Execution flow

1. **Confirm address + date** (ask if missing) and confirm an image is attached.
2. **Read the image with native vision**; parse every legible row into First / Last / Email / Phone /
   Notes. Flag ambiguous cells with `⚠` and a trailing `?`.
3. **Render the numbered review table** using the Output format. Explicitly ask the agent to correct
   handwriting misreads — call out the `⚠` rows by number.
4. **Wait for explicit confirmation.** Apply every correction the agent makes. If they don't confirm,
   **write nothing.** This is the hard gate.
5. **On "confirm", write each contact** in order:
   - De-dupe first — `fub_search_contacts` on the email (then phone) to detect an existing match.
     Note matches as **merged/duplicate** rather than treating them as brand-new.
   - `fub_create_lead` with `firstName`, `lastName?`, `email?`, `phone?`,
     `source: "Open House"`, `tags: ["Open House"]`, and
     `message: "[address] open house, [date]"`. (FUB merges into the existing contact when the
     email/phone already exists, so it's safe to call per row.)
6. **Report the summary** — N created, M merged/duplicates, K skipped — and offer the obvious next
   step: *"Want me to draft a follow-up to this batch?"* (hands off to **follow-up**).

## Write safety

- The **only** write tool here is `fub_create_lead`. It fires **once per confirmed row, after the
  agent reviews the full table and says "confirm"** — never before.
- `fub_search_contacts` is read-only; use it freely for de-duping during the write phase.
- Treat low-confidence cells as **blockers worth surfacing**, not silent best-guesses — a wrong email
  means a lead never gets reached. Always show the `⚠` rows and let the agent fix them.
- Put only what was on the sheet into Notes. Never add neighborhood demographics or any protected-class
  characteristic (Fair Housing) — capture the home/process facts the lead wrote down, nothing more.

## Related skills

- **follow-up** — draft the first nudge to everyone you just captured (the natural next move after a
  successful import).
- **lead-reply** — when one of these new leads replies, respond in the agent's voice with full context.
- **daily-briefing** — newly captured open-house leads show up in tomorrow's priorities.
