# Template: Showing Feedback Summary

## When to Use
Seller-only. Sent during `active_listing` stage to summarize showing activity over a period — typically weekly, or after a cluster of showings. Keeps the seller informed and sets realistic expectations about buyer interest and feedback patterns.

## Context to Read
- Showing data from context passed by orchestrator — number of showings in the period, feedback themes collected from buyer's agents
- `asking_price_target` / agreed list price — feedback often touches on price
- Days on market — how long the property has been listed
- `contact_log` Running Timeline — what was reported in the last showing summary, so this one can show a trend
- `constraints.tags` — any urgency factor (e.g., `relocation` means slow activity has real timeline consequences)

## Subject Line
`Showing Update — [Street Address]`

## Required Content (in order)

1. **Activity number** — how many showings in the period. Simple statement of fact.

2. **Feedback themes** — what buyers and their agents said. Do not sanitize. If the feedback is that the kitchen feels dated or that the price seems high for the street, say it. Group recurring themes together. Sellers need accurate feedback to make decisions — sugarcoating it delays the inevitable.

3. **Context / interpretation** — brief. What does the activity level and feedback mean? Is the pace normal for this price point and area? Are there patterns worth noting? One to three sentences.

4. **Comparison** (if available) — how does showing activity compare to similar homes in the area right now? Useful context for the seller to calibrate their expectations.

5. **What the agent is doing** — one sentence. Anything active: following up with buyer's agents, adjusting syndication, open house scheduled, etc.

6. **Next update or action** — when the next summary is expected, or if there is a decision point approaching (e.g., "if activity doesn't improve in the next week, I'd like to have a pricing conversation").

## Tone Notes
- Honest. The most valuable showing feedback is the kind the seller does not want to hear. This is where trust is built or broken.
- Calm and data-grounded. Present the feedback without alarm, but without minimizing either.
- If the feedback points toward a price issue, plant that seed here. Do not force a full price reduction conversation in this email — that is the `price_reduction_discussion` template — but do not hide the signal either.

## Do Not Include
- Only positive feedback while omitting negative — report both
- Vague summaries like "buyers had mixed reactions" — be specific about what was said
- A price reduction recommendation in this email — if that is needed, trigger the `price_reduction_discussion` template separately
