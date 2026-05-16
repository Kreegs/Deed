# Property Research — Handoff

## What the Property Research Specialist Receives

The orchestrator passes the following when routing here. All fields are required unless noted.

**For buyer property research:**
```
client_name:         [string]
intent:              "buy"
address_of_interest: [address of the specific property to research]
property_folder:     clients/open/[client_name]/properties/[address_slug]/
lead_object_path:    clients/open/[client_name]/lead_object.json
contact_log_path:    clients/open/[client_name]/contact_log.md
```

**For seller CMA:**
```
client_name:         [string]
intent:              "sell"
address_of_interest: [the property being listed — from lead_object.address_of_interest]
lead_object_path:    clients/open/[client_name]/lead_object.json
contact_log_path:    clients/open/[client_name]/contact_log.md
```

The specialist reads the full lead object at `lead_object_path` before starting. The context it needs — budget, asking price target, location preferences, constraint tags, timeline — is all there.

---

## What the Property Research Specialist Produces

### Buyers — Property Report

**Written to:** `clients/open/[client_name]/properties/[address_slug]/property_report.md`

Sections (in order):
1. Property Overview — address, MLS, list price, beds/baths/sqft/year built, days on market, price history
2. Fit Assessment — how the property measures against this buyer's budget, location preferences, timeline, and constraint tags
3. Market Context — 3–5 comparable sales, price/sqft comparison, market velocity assessment
4. Condition Notes — listing disclosures, age-related flags, any known issues
5. Local Knowledge — from `community_notes.md` if matching entries exist; omitted if none
6. Summary — 2–4 sentences on what stands out, whether the price is defensible, and what the agent should raise before scheduling a showing

**After writing the report, the specialist updates `property_object.json`:**

```json
{
  "mls_number":       [populated if found],
  "list_price":       [populated if found],
  "property_details": {
    "beds":           [populated if found],
    "baths":          [populated if found],
    "sqft":           [populated if found],
    "year_built":     [populated if found],
    "property_type":  [populated if found]
  },
  "listing_url":      [preserved if already set; do not overwrite with null],
  "main_image_url":   [primary listing image URL extracted from listing_url page, or null if listing_url was not set],
  "research_complete": true,
  "last_updated":     [today's date]
}
```

`research_complete` must be set to `true` before returning. This is how the orchestrator detects completion.

---

### Sellers — CMA Report

**Written to:** `clients/open/[client_name]/cma_report.md`

Sections (in order):
1. Subject Property — address, known specs, seller's asking price target
2. Comparable Sales — table of 4–6 recent comps (address, sale price, sq ft, $/sqft, beds/baths, days on market, close date)
3. Market Summary — average price, average $/sqft, implied value of subject property, average DOM, market direction
4. Suggested List Price Range — derived from comps, with rationale
5. Seller's Target vs. Market — comparison and direct assessment (on target / slightly high / significantly above)
6. Constraint Considerations — only if constraint tags are present that affect pricing or timing strategy
7. Local Knowledge — from `community_notes.md` if matching entries exist; omitted if none
8. Summary — 3–5 sentences with the data-grounded recommendation for the agent before the listing conversation

There is no `research_complete` flag to set for sellers. The presence of `cma_report.md` signals completion.

---

## contact_log Updates

The specialist appends one entry to the **Running Timeline** and overwrites the **Current State** section before returning.

**Running Timeline entry format:**
```
[date] — Property Research — [Report type] complete for [address]. [1–2 sentences: key finding and any flag the agent should know before acting on the output.]
```

**Current State overwrite:**
```
Current stage:     [mirrors stage in lead_object.json — do not change it]
Last action:       [Buyer property report / Seller CMA] complete for [address]
Next action:       [Suggested by the research output — e.g., "Review report with agent before scheduling showing" or "Review CMA with Diana before listing conversation"]
Open questions:    [Any specific question the report raises — e.g., "Seller's target is $30K above comps — pricing conversation needed"]
Deadline:          [From lead_object.timeline if relevant, otherwise "None identified"]
```

---

## What the Orchestrator Does with the Output

After the specialist returns, the orchestrator:

1. Confirms `research_complete: true` is set in `property_object.json` (buyers only).
2. Confirms the report file was written.
3. Pauses at the human gate: *"Research complete for [address]. Do you want to draft a first contact email?"*

The orchestrator does not auto-chain to the communication specialist. It waits for the agent to confirm.
