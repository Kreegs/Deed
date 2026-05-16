# Template: General Update

## When to Use
Catch-all progress update for either buyers or sellers at any stage. Used when the agent wants to check in, close a communication loop, or share a status update that does not have a more specific template. Also used when a status check is warranted by stage stale thresholds (see `/status` command).

## Context to Read
- `deal_stage` — shapes what an update at this stage should cover
- `contact_log` Current State — last action completed, next action, open questions, deadlines
- `contact_log` Running Timeline — last few entries; what has happened recently
- Any deadlines or open items in the Document Checklist
- `constraints.tags` — anything creating urgency

## Subject Line
`Update — [Client first name] / [Address or deal nickname]`

## Required Content (in order)

1. **Where things stand** — one to two sentences. What stage are they in and what is actively happening. Reference the most recent action from the contact_log.

2. **What is coming next** — the immediate next step and roughly when. Buyers want to know what they are waiting on. Sellers want to know what the agent is doing. Be specific.

3. **Open items** (if any) — anything the client needs to know about or respond to. If there is nothing open, say so — "nothing needed from you right now" is reassuring.

4. **Deadline or timing note** (if applicable) — if there is a time-sensitive item in the next few days, surface it clearly.

5. **Close** — brief. Invite response if anything is unclear.

## Tone Notes
- Match the tone to the stage. A general update at `due_diligence` with a week until close should feel more urgent than one at `property_search` with no deadlines.
- "Nothing to report" is a valid update if everything is on track — but it should still be one sentence, not an apology for reaching out.
- Short is better. If there is not much to say, do not pad.

## Do Not Include
- Full recap of the entire deal history — Current State and recent timeline only
- Items that would be better handled by a specific template (inspection findings, price reduction, etc.) — use the right template for those
