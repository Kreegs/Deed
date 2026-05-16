# Template: Counter Offer Response

## When to Use
Relaying a seller's counter-offer to a buyer and helping them understand their options. Used during `offer_pending` stage when the seller responds with a counter rather than accepting or rejecting.

## Context to Read
- `address_of_interest` — the property
- Original `offer_price` from `property_object.json`
- Counter terms from context passed by orchestrator (counter price, any changed contingencies or close date)
- `budget_range.max` — is the counter within the buyer's stated maximum?
- `contact_log` — what the buyer's priorities were going in (price sensitivity, close date flexibility, contingencies)
- `constraints.tags` — anything that affects how the buyer should think about the counter

## Subject Line
`Counter Offer — [Address]`

## Required Content (in order)

1. **Counter terms** — state the counter clearly: price, and any other terms that changed (close date, contingencies, items excluded, etc.). Be factual. Do not editorialize yet.

2. **Compare to original offer** — one line showing the delta. "They came down $8,000 from list but held firm on the close date."

3. **Buyer's options** — clearly lay out three paths: accept, reject, or counter back. Do not skip any. Give the buyer all options even if one is clearly not viable.

4. **Brief framing** (optional, agent judgment) — if the agent wants to offer a perspective on what makes sense, include one sentence. Keep it advisory, not directive. "Given your August deadline, the close date matters more than the $5K gap" is appropriate. "Take it" is not.

5. **Response deadline** — how long the buyer has to respond if a deadline was stated. If no deadline was given, ask the buyer how quickly they want to move.

6. **Close** — ask the buyer to call or respond with their direction.

## Tone Notes
- This is a decision point. The tone should be clear and calm, not urgent or anxious.
- Present the counter factually before adding any framing. The buyer needs to see the terms before they hear the agent's take.
- Acknowledge that counters can be frustrating, especially if the gap is large — but do not project disappointment. Keep it constructive.

## Do Not Include
- Speculation about the seller's motivation or bottom line
- Strong recommendations unless the agent specifically wants them in the email
- Pressure language ("you need to decide now" unless there is an actual deadline)
