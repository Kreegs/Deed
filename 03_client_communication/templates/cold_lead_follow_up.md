# Template: Cold Lead Follow-Up

## When to Use
Either side. Used to re-engage a lead or client that has gone quiet — has not responded to prior outreach, paused their search or sale, or simply dropped off. Triggered by stale thresholds in `/status` or by agent request. The goal is to re-open the conversation, not to close a deal in one email.

## Context to Read
- `deal_stage` — where the lead was when they went quiet; this shapes the re-engagement framing
- `contact_log` Running Timeline — the last few interactions; what was the last thing that happened before they went quiet?
- `contact_log` Current State — what was supposed to happen next that never did?
- Days since last contact — duration of silence shapes tone (2 weeks is different from 3 months)
- `constraints.tags` — anything that may explain the silence (e.g., `must_sell_first` — their sale may have stalled; `lease_expiring` — their situation may have changed; `relocation` — plans may have shifted)
- `lead_source` — referrals get a slightly warmer re-engagement than cold leads

## Subject Line
- Short silence (2–4 weeks): `Checking In — [First name]`
- Longer silence (1–3 months): `Still Here If You Need Anything — [First name]`
- Very long silence (3+ months): `Wanted to Reach Out — [First name]`

## Required Content (in order)

1. **Low-pressure opening** — acknowledge the silence without making the client feel guilty for it. Do not open with "I haven't heard from you." Open with something that references where the conversation left off: "Last we talked, you were deciding between the two properties on Oak Ave — wanted to check in and see where things landed."

2. **Reference the last meaningful touchpoint** — one sentence from the contact_log. Show that you remember where things were. This is what separates a personal re-engagement from a generic drip email.

3. **Brief market note or relevant update** (optional but effective) — if something has changed in the market that is relevant to their search or listing, mention it. "Inventory has opened up a bit in [their target area] — there are a few new properties worth a look." Or: "A similar home on your street just sold for $505K, which is useful context for where your property sits." This gives the client a reason to re-engage beyond just the relationship.

4. **Low-pressure offer** — one option. Not a list of things they can do. One: "If you're still thinking about it, I'm happy to pick up where we left off." Or: "If timing has shifted for you, no pressure — just want you to know I'm here when you're ready." Match the offer to the length of the silence and the agent's sense of where the client is.

5. **Close** — brief. No call to action pressure. "Let me know if anything has changed" is enough.

## Tone Notes
- This should feel like a message from someone who actually remembers you, not a template. The contact_log reference is what makes it feel personal.
- Never make the client feel guilty for going quiet. People's lives change. The goal is to be the person they call when they are ready, not the person they avoid because they feel bad for not responding.
- Match length to silence duration. A two-week check-in is a short email. A three-month re-engagement is still short but slightly warmer and more personal.
- If the client has gone very cold (3+ months, no response to multiple attempts), this may be the last outreach before moving them to `"cold"` status. That does not change the tone — it just means the close should leave the door open graciously.

## Do Not Include
- "I've tried reaching you several times" — creates guilt and defensiveness
- Multiple calls to action
- Market statistics that are not directly relevant to their situation
- Any tone that implies urgency the client has not indicated
