# Template: Offer Received Summary

## When to Use
Seller-only. Used at `offer_received` stage to present an incoming offer to the seller. The seller needs clear, complete information to make a decision. This email presents the offer terms and outlines their options.

## Context to Read
- Offer terms from context passed by orchestrator: offer price, earnest money, down payment (if disclosed), financing contingency, inspection contingency, close date, possession date, any unusual terms or exclusions
- Agreed list price — the delta between list and offer is the first thing the seller will notice
- `close_date` preference — does the offered close date work for the seller's timeline?
- `constraints.tags` — anything that affects how the seller should weigh the offer (e.g., `must_sell_first` means close date flexibility matters more than price; `relocation` means timeline is firm)
- `contact_log` — any prior discussion about what the seller would accept or prioritize

## Subject Line
`Offer Received — [Address]`

## Required Content (in order)

1. **The offer — lead with the number.** State the offer price clearly in the first sentence. Do not bury it.

2. **Full terms summary** — structured and complete:
   - Offer price
   - Earnest money amount
   - Financing contingency (yes/no; if yes, loan type and amount if disclosed)
   - Inspection contingency (yes/no; any limitations)
   - Close date as proposed
   - Possession date if different from close
   - Any unusual terms, exclusions, or requests

3. **Comparison to list price** — the gap, stated plainly. "$12,000 below list" or "at asking price." No editorializing yet — just the fact.

4. **Seller's options** — lay out all three:
   - **Accept** — the deal closes on these terms
   - **Counter** — propose different terms (price, close date, contingencies, etc.)
   - **Reject** — decline without countering

5. **Agent's recommendation** (if agent wants it in the email) — one sentence, advisory framing. "Given your timeline, the close date they proposed works well — the price gap is the only thing to decide." Do not push. The seller decides.

6. **Response deadline** — when does the offer expire? State it clearly.

7. **Close** — ask the seller to call or respond with their direction.

## Tone Notes
- Factual and organized. The seller needs to understand the offer, not feel managed through it.
- Present the offer before interpreting it. The seller should see the terms before they hear the agent's take.
- Deadlines matter. If the offer expires in 24 hours, that needs to be prominent, not buried.

## Do Not Include
- Speculation about whether there are other offers coming or buyer motivation
- Strong directional recommendation unless the agent specifically wants it in the email
- Anything that sounds like pressure to accept or reject
