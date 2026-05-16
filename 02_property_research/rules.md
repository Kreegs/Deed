# Property Research — Rules

## General Rules (Apply to Both Buying and Selling)

**Read the full lead object before starting.** The lead object contains everything that shapes the research: intent, budget, asking price target, location preferences, constraint tags, and timeline. Do not produce a generic report — calibrate every output to this specific client.

**Check `community_notes.md` for every report.** Match against `location_preferences`, `address_of_interest`, and any zip codes associated with the area being researched. Pull only entries relevant to the client's areas — not the whole file. If no matching entries exist, omit the Local Knowledge section entirely. Do not note its absence.

**Update `contact_log.md` when research is complete.** Append one Running Timeline entry and overwrite the Current State section. Research is not complete until the log is updated.

**Do not editorialize beyond what the data supports.** Flag concerns clearly when they exist (price mismatch, condition issues, market risk), but stay factual. The agent and client make the decision.

---

## Buying

### When This Runs

The orchestrator routes here when a buyer's lead object has `research_complete: false` on a property subfolder. This can happen at multiple points in the deal — qualification (if a specific property came in with the lead), or any time the agent says "research [address] for [client]."

### What to Read Before Starting

- `lead_object.json` — budget_range, location_preferences, pre_approved, timeline, constraint tags
- `properties/[address_slug]/property_object.json` — address, any known property details
- `community_notes.md` — any entries matching the property's neighborhood or zip code

### Property Report Structure

Write to `clients/open/[client_name]/properties/[address_slug]/property_report.md`.

```
# Property Report — [Address]
Prepared for: [client_name]  |  [date]

## Property Overview
- Address, MLS number (if known), list price
- Beds / baths / sq ft / year built / property type
- Days on market, list date
- Price history (any reductions, and when)

## Fit Assessment
How this property measures against the client's stated criteria:
- Budget: [list price vs budget_range — within range / over / under by how much]
- Location: [match against location_preferences]
- Timeline: [any factors that affect when a buyer could move in — vacant, tenant-occupied, etc.]
- Constraint flags: [call out anything from constraint tags that affects this property — e.g., if client has must_sell_first, note if seller will need a long close or is flexible]

## Market Context
- Recent comparable sales: 3–5 similar properties that sold within the last 6 months, within reasonable distance. For each comp: address, sale price, sq ft, price/sqft, days on market, close date.
- Subject property price per square foot vs. comp average
- Whether the list price appears justified, high, or low relative to comps
- Average days on market in this area and how the subject compares

## Condition Notes
- Anything notable from the listing description, photos, or disclosed history (age of roof, HVAC, foundation disclosures, etc.)
- Older homes (pre-1990): flag that inspection findings are likely — advise factoring repair credits or budget into offer strategy
- Flag any disclosed issues or known red flags

## Local Knowledge
[Include only if community_notes.md has a matching entry for this neighborhood or zip code]
[Label clearly as agent notes, not market data]

## Summary
2–4 sentences. What stands out — positive and negative. Whether the price looks reasonable. Any specific consideration the agent should raise with the client before scheduling a showing.
```

### After Writing the Report

1. Set `research_complete: true` in `properties/[address_slug]/property_object.json`.
2. Update `property_object.json` with any fields now known: `mls_number`, `list_price`, `property_details` (beds/baths/sqft/year_built/property_type).
3. Update `contact_log.md`.

### Multiple Properties

If a buyer has multiple properties with `research_complete: false`, research each one. Each gets its own report in its own subfolder. The contact_log entry covers all properties researched in that session.

---

## Selling

### When This Runs

The orchestrator routes here automatically when a seller's lead object has `status: "qualified"` and `address_of_interest` is non-null. The output is a CMA, not a property report.

### What to Read Before Starting

- `lead_object.json` — address_of_interest, asking_price_target, timeline, constraint tags (especially estate_sale, divorce_sale, 1031_exchange — these affect pricing urgency)
- `community_notes.md` — any entries matching the property's neighborhood or zip code

### CMA Structure

Write to `clients/open/[client_name]/cma_report.md`.

```
# Comparative Market Analysis — [Address]
Prepared for: [client_name]  |  [date]
Assigned agent: [assigned_agent]

## Subject Property
- Address, estimated sq ft (from public records or listing history), beds/baths, year built, property type
- Seller's target price: $[asking_price_target]

## Comparable Sales
Recent sales of similar properties — ideally within 0.5 miles, similar sq ft (±20%), same property type, sold within the last 6 months. If the local market is thin, expand the radius or time window and note that you did.

For each comp (aim for 4–6):
| Address | Sale Price | Sq Ft | $/Sq Ft | Beds/Baths | Days on Market | Close Date |
|---|---|---|---|---|---|---|

## Market Summary
- Average sale price of comps: $[X]
- Average price per sq ft: $[X]
- Subject property implied value at comp average: $[X] (sq ft × avg $/sqft)
- Average days on market for comps: [X] days
- Direction of the market in this area: [rising / flat / softening — based on comp trends]

## Suggested List Price Range
Based on the above: $[low] – $[high]

Rationale: [2–3 sentences explaining how you arrived at this range — what drove it up or down relative to comps, any condition assumptions, market velocity, or constraint factors that affect pricing strategy]

## Seller's Target vs. Market
- Seller's asking price target: $[asking_price_target]
- Suggested range: $[low] – $[high]
- Assessment: [On target / Slightly high — may need X days to find the right buyer / Significantly above comps — recommend a pricing conversation before listing]

## Constraint Considerations
[Only include if constraint tags are present that affect pricing or timing]
- estate_sale: Multiple decision-makers may slow offer response. Price should be competitive enough to avoid a drawn-out listing.
- divorce_sale: Time pressure often means accepting market value rather than holding for top dollar.
- must_sell_first: Flexibility on close date may compensate for a lower offer price.
- 1031_exchange: Hard deadlines on the exchange may require a faster close — price accordingly.
- relocation: Hard move date creates urgency. Overpricing risks a stale listing and a forced reduction.

## Local Knowledge
[Include only if community_notes.md has a matching entry for this neighborhood or zip code]
[Neighborhood character affects buyer pool and pricing expectations — include relevant context]

## Summary
3–5 sentences. What the data says, how the seller's target compares, and any specific recommendation for Diana or the assigned agent before the listing conversation.
```

### After Writing the CMA

Update `contact_log.md`. There is no `research_complete` flag to set for sellers — the CMA file itself signals completion.
