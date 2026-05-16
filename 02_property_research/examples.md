# Property Research — Examples

Two worked examples: one buyer property report, one seller CMA. Each shows the full input-to-output flow.

---

## Example 1: Buyer Property Report

**Context:** Kevin Marsh is a qualified buyer, assigned to Sarah. He came in through Zillow interested in 204 Creekside Dr, Millbrook. Budget max is $390K, pre-approved, lease expiring August 2026.

**Received from orchestrator:**
```
client_name:         Kevin Marsh
intent:              buy
address_of_interest: 204 Creekside Dr, Millbrook
property_folder:     clients/open/kevin_marsh/properties/204_creekside_dr/
lead_object_path:    clients/open/kevin_marsh/lead_object.json
contact_log_path:    clients/open/kevin_marsh/contact_log.md
```

**Lead object fields read before starting:**
- `budget_range.max`: 390000
- `pre_approved`: true
- `timeline`: "Must be in by August 2026 — lease expiring"
- `location_preferences`: ["Cedarville", "Millbrook"]
- `constraints.tags`: ["lease_expiring"]
- `community_notes.md` check: No entries for Millbrook. Local Knowledge section omitted.

---

**Report written to `clients/open/kevin_marsh/properties/204_creekside_dr/property_report.md`:**

```markdown
# Property Report — 204 Creekside Dr, Millbrook
Prepared for: Kevin Marsh  |  2026-05-12

## Property Overview
- **Address:** 204 Creekside Dr, Millbrook
- **MLS:** 7841293
- **List price:** $374,900
- **Beds / Baths:** 3 bed / 2 bath
- **Sq ft:** 1,842  |  **Year built:** 2019  |  **Type:** Single family
- **Days on market:** 12
- **Price history:** Listed at $379,900 on 04/14/2026. Reduced $5,000 on 04/28/2026.

## Fit Assessment
- **Budget:** $374,900 list price — within Kevin's max of $390K with $15K of room for inspection
  credits or minor negotiation.
- **Location:** Millbrook — matches stated preference. The Creekside subdivision is approximately 2.5 miles from the Millbrook city center.
- **Timeline:** Listing shows vacant / move-in ready. No tenant situation — this property
  supports a close well before Kevin's August deadline.
- **Constraint flags:** `lease_expiring` — urgency is moderate (3+ months). Not a fire-drill
  situation, but Kevin should not stall. If the property suits him, moving within 30 days is
  reasonable.

## Market Context
Comparable sales — 3 bed / 2 bath single family, Millbrook area, last 6 months:

| Address | Sale Price | Sq Ft | $/Sq Ft | DOM | Close Date |
|---|---|---|---|---|---|
| 118 Stonecreek Blvd | $368,000 | 1,790 | $205.59 | 8 | 2026-03-20 |
| 341 Ridgecrest Loop | $381,500 | 1,880 | $202.93 | 22 | 2026-02-14 |
| 57 Millbrook Cir | $362,000 | 1,755 | $206.27 | 5 | 2026-01-08 |
| 892 Sunvale Dr | $377,000 | 1,900 | $198.42 | 31 | 2025-12-03 |

- **Comp average sale price:** $372,125
- **Comp average $/sqft:** $203.30
- **Subject implied value at comp avg:** 1,842 × $203.30 = $374,477
- **Assessment:** List price of $374,900 is essentially at market. The $5K reduction suggests
  the sellers are motivated and not holding firm at the original ask. No significant premium
  being demanded.
- **Average comp DOM:** 16.5 days. Subject at 12 days is moving at normal pace — not stale,
  not flying off the market.

## Condition Notes
- Built 2019 — relatively new construction. Lower risk of major system issues.
- Listing describes original owners, no disclosures flagged.
- No known foundation or flood zone issues in this subdivision per public records.
- Standard recommendation: schedule an inspection regardless — even newer homes have findings.

## Summary
204 Creekside Dr is priced at market, move-in ready, and fits Kevin's budget with room. The
2019 build year and 12 days on market suggest a clean property without urgency pressure.
Comps support the ask. If Kevin likes what he sees in person, an offer at or just below list
is defensible. Sarah should schedule a showing soon — nothing about this listing screams
imminent competition, but it won't sit indefinitely either.
```

---

**`property_object.json` updated:**
```json
{
  "address": "204 Creekside Dr, Millbrook",
  "mls_number": "7841293",
  "list_price": 374900,
  "status": "active",
  "property_details": {
    "beds": 3,
    "baths": 2,
    "sqft": 1842,
    "year_built": 2019,
    "property_type": "single_family"
  },
  "showing_dates": [],
  "buyer_notes": null,
  "offer_price": null,
  "research_complete": true,
  "added_date": "2026-05-12",
  "last_updated": "2026-05-12"
}
```

**contact_log update (Running Timeline):**
```
2026-05-12 — Property Research — Buyer report complete for 204 Creekside Dr, Millbrook. Priced at
market ($374,900), move-in ready, comp-supported. No red flags. Ready for showing.
```

**contact_log update (Current State):**
```
Current stage:     property_search
Last action:       Buyer property report complete for 204 Creekside Dr
Next action:       Sarah to review report and schedule showing with Kevin
Open questions:    None — property is clean and within budget
Deadline:          Kevin's lease expires August 2026
```

---

## Example 2: Seller CMA

**Context:** Patricia Odom is a qualified seller, assigned to Diana. She wants to list her late mother's home at 812 Ridgewood Trail, Westbrook. Asking price target is $520,000. Constraint tags: relocation, estate_sale.

**Received from orchestrator:**
```
client_name:         Patricia Odom
intent:              sell
address_of_interest: 812 Ridgewood Trail, Westbrook
lead_object_path:    clients/open/patricia_odom/lead_object.json
contact_log_path:    clients/open/patricia_odom/contact_log.md
```

**Lead object fields read before starting:**
- `asking_price_target`: 520000
- `timeline`: "90 days"
- `constraints.tags`: ["relocation", "estate_sale"]
- `constraints.notes`: "Patricia is executor of her mother's estate. Existing mortgage but sufficient equity. No buy-side needed."
- `community_notes.md` check: No entries for Westbrook. Local Knowledge section omitted.

---

**Report written to `clients/open/patricia_odom/cma_report.md`:**

```markdown
# Comparative Market Analysis — 812 Ridgewood Trail, Westbrook
Prepared for: Patricia Odom  |  2026-05-12
Assigned agent: Diana

## Subject Property
- **Address:** 812 Ridgewood Trail, Westbrook
- **Estimated specs:** 3 bed / 2 bath, approx. 1,650 sq ft (per county appraisal records),
  year built 1978, single family
- **Seller's target price:** $520,000

## Comparable Sales
Single family, 3 bed / 2 bath, Westbrook area, 1,400–1,900 sq ft, last 6 months:

| Address | Sale Price | Sq Ft | $/Sq Ft | Beds/Baths | DOM | Close Date |
|---|---|---|---|---|---|---|
| 934 Westgate Blvd | $498,000 | 1,620 | $307.41 | 3/2 | 19 | 2026-04-10 |
| 1105 Scenic Dr | $511,000 | 1,700 | $300.59 | 3/2 | 7 | 2026-03-28 |
| 427 Ridgewood Trail | $489,500 | 1,590 | $307.86 | 3/2 | 25 | 2026-02-15 |
| 78 Clearwater Ln | $524,000 | 1,760 | $297.73 | 3/2.5 | 11 | 2026-01-30 |
| 2201 Barranca Dr | $505,000 | 1,680 | $300.60 | 3/2 | 18 | 2025-12-12 |

## Market Summary
- **Average sale price:** $505,500
- **Average price per sq ft:** $302.84
- **Subject implied value at avg $/sqft:** 1,650 × $302.84 = $499,686
- **Average days on market:** 16 days
- **Market direction:** Flat to slightly softening. The highest comp ($524K) went in January and
  was a slightly larger home. More recent comps have come in at $489K–$511K. Pricing at the
  top end requires a condition premium.

## Suggested List Price Range
**$495,000 – $515,000**

Rationale: Comps cluster between $489K and $524K for this size and zip, with the subject's
1,650 sq ft landing near the middle of that range. A 1978 build will likely carry inspection
findings — buyers will expect some concession or price flexibility. The $515K ceiling applies
only if the home shows well and has been updated (kitchen, baths, HVAC). The $495K floor is
appropriate if systems are original or condition is dated.

## Seller's Target vs. Market
- **Seller's target:** $520,000
- **Suggested range:** $495,000 – $515,000
- **Assessment:** Slightly above comps. $520K is achievable only if the home is in strong
  condition and updates are visible. At $520K on a 1978 estate sale, buyers will negotiate
  harder and inspectors will find more. Recommend Diana have a direct conversation about
  condition before committing to a list price. If Patricia can accept $510K as a likely
  outcome, list at $519K or $520K and let buyers negotiate in. If $520K is a firm floor,
  that needs to be surfaced before listing.

## Constraint Considerations
- **estate_sale:** Patricia is the executor. Offer response may require coordination with
  legal counsel or other beneficiaries — build extra response time into the listing
  conversation. Buyers should be aware this is an estate sale, as it typically signals
  as-is preferences and sets expectations appropriately.
- **relocation:** 90-day timeline to list and close creates moderate urgency. Overpricing
  risks a stale listing and a forced reduction — Diana should be direct about this tradeoff
  with Patricia.

## Summary
Market data supports a list price of $495K–$515K. Patricia's $520K target is within reach
but requires the home to show at the high end of the comp range. Given the estate sale
context and 1978 build, Diana should walk the property before committing to a list price —
condition is the deciding variable here. The relocation timeline is manageable but leaves
no room for a slow start. Recommend pricing at $515K if condition justifies it, with a clear
conversation about the likely negotiated outcome before Patricia signs the listing agreement.
```

---

**contact_log update (Running Timeline):**
```
2026-05-12 — Property Research — CMA complete for 812 Ridgewood Trail. Comps support
$495K–$515K. Patricia's $520K target is slightly above market — condition will determine
whether it's achievable. Diana needs to walk the property before setting list price.
```

**contact_log update (Current State):**
```
Current stage:     listing_prep
Last action:       CMA complete for 812 Ridgewood Trail
Next action:       Diana to review CMA and walk the property before listing conversation with Patricia
Open questions:    Property condition unknown — is the 1978 build updated or original?
                   $520K target may need adjustment depending on what Diana sees.
Deadline:          Patricia wants to list within 90 days (by ~2026-08-12)
```
