---
name: remember
description: >
  Self-improving memory for the agent's preferences, rules, and business facts. Use this skill
  whenever the agent states a durable instruction or correction — phrases like "don't do that",
  "stop doing X", "from now on", "always", "never", "remember that", "my brokerage is", "my
  service area is", "I don't work Sundays", "use this signature" — or asks "what have you learned
  about me" or "forget that". Persists the rule so it applies in every future session.
metadata:
  version: "1.0.0"
---

# Remember

Make the system smarter every time the agent corrects it or states a preference. A rule said once
should apply forever, automatically, across every skill — the agent should never have to repeat
themselves.

## How to talk to the agent

Write for a busy real estate agent — not a developer, marketer, or AI expert. Detailed but concise and skimmable:
- **Plain language.** No technical or insider jargon. If a term is unavoidable, define it in a few plain words.
- **Lead with what matters to them** — leads, listings, clients, money, time saved — not the mechanics behind it.
- **Tight and scannable.** Short sentences, bullets, clear headers. Detailed enough to act on, never padded.
- **End with the clear next step.**

## When this fires

Automatically, whenever the agent expresses a durable instruction, not a one-off request:

- **Rules / corrections:** "don't ever cold-text leads", "stop signing emails with my full name", "always include the address in listing posts", "from now on, follow up within 1 hour"
- **Business facts:** "my brokerage is Compass", "my license # is 0123456", "I serve the South Bay", "my office hours are 9–6", "my niche is first-time buyers"
- **Management:** "what have you learned about me?" (recall), "forget that I said X" (remove)

If the agent is making a one-time request ("reply to this lead"), do **not** treat it as a memory — just do the task.

## Where memory lives

Two tiers, both saved in the agent's **selected working folder** (in Cowork the agent runs from a
plugin cache directory, so always write to the working folder, never a relative path):

- **`CLAUDE.md`** — the quick-reference file, kept short (~50–80 lines). Holds business facts + the most important rules. Every skill reads this first.
- **`memory/`** — deeper storage for detail and extras (e.g. `memory/preferences.md`, `memory/business.md`). Grows without crowding the quick-reference file.

> If no working folder is selected, tell the agent: *"Pick a working folder so I can save this and remember it next time,"* then proceed once they do (or save for this session only if they decline).

## `CLAUDE.md` format

Keep it scannable. Create it if missing.

```markdown
# Memory

## Business
- Agent: [Name], [Brokerage], license [#]
- Service area: [areas]
- Niche: [e.g. first-time buyers, luxury]
- Hours: [e.g. 9–6, no Sundays]

## Rules (do / don't)
- [ ] DO: follow up with new leads within 1 hour
- [ ] DON'T: cold-text leads — email first
- [ ] Email sign-off: "— [First], [Brokerage]"

## Preferences
- [Tone/process notes that aren't voice-specific]
```

## Execution flow

1. **Classify** the agent's statement: business fact, rule (do/don't), preference, or a recall/forget request.
2. **Recall** request → read `CLAUDE.md` (and `memory/` if needed) and summarize what's stored.
3. **Forget** request → locate the entry, remove it, confirm what was removed.
4. **New rule/fact:**
   - Read the current `CLAUDE.md`. If it doesn't exist, create it with the template above.
   - Add or update the relevant line. Keep `CLAUDE.md` under ~80 lines — move detail/overflow into `memory/`.
   - If it's a content/writing-style preference (tone, phrasing, emoji, hashtags), note that it also belongs in the voice profile and offer to update it via **learn-my-voice**.
5. **Confirm** in one line what you saved and where (e.g. *"Saved: never cold-text leads → CLAUDE.md › Rules."*).

## Division of labor

- **`CLAUDE.md` (this skill):** how the system should *behave* + business facts.
- **`voice-profile.md` (learn-my-voice):** how the agent *writes*.

Content-style corrections feed the voice profile; operating rules and facts feed `CLAUDE.md`. When in doubt, save the rule here and cross-reference.

## Tips

- Prefer small, specific rules over vague ones ("follow up within 1 hour" beats "be responsive").
- When a saved rule conflicts with a new instruction, surface the conflict and ask which wins, then update.
- Never store secrets (passwords, full API keys) in memory files.
