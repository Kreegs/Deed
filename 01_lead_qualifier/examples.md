# Lead Qualifier — Examples

Two worked examples: one buyer, one seller. Each shows the qualifier receiving a partially populated lead object and completing it through a focused intake conversation.

---

## Example 1: Buyer Qualification

**Context:** The orchestrator received a Zillow form submission for Kevin Marsh and pre-populated the lead object with what was in the form. The qualifier receives the object below and must complete it.

**Lead object as received (pre-populated by orchestrator):**
```json
{
  "client_name": "Kevin Marsh",
  "contact_info": {
    "email": "kevin.marsh@gmail.com",
    "phone": "555-201-0187",
    "preferred_method": null
  },
  "intent": "buy",
  "status": "pending_info",
  "stage": "qualifying",
  "lead_source": "zillow",
  "referral_from": null,
  "pre_approved": null,
  "budget_range": {
    "min": null,
    "max": null
  },
  "address_of_interest": "204 Creekside Dr, Millbrook",
  "timeline": null,
  "location_preferences": ["Cedarville", "Millbrook"],
  "constraints": {
    "tags": [],
    "notes": null
  },
  "assigned_agent": null,
  "recommended_next_action": "",
  "created_date": "2026-05-12",
  "last_updated": "2026-05-12"
}
```

**Fields already populated:** client_name, both contact fields, intent, lead_source, address_of_interest, location_preferences.

**Fields still needed:** preferred_method, pre_approved, budget_range, timeline, assigned_agent, constraint tags, recommended_next_action.

---

**Qualifier output — intake questions asked:**

> "Kevin came in through Zillow with a specific property in mind — 204 Creekside Dr in Millbrook. A few things I need to complete his profile:
>
> - What's his budget range? Specifically need a max to get property research started.
> - Has he been pre-approved, is he in process with a lender, or has he not started yet?
> - What's his timeline — when does he need to be in a home?
> - How does he prefer to be contacted?
> - Any agent assignment preference, or should this go to whoever has capacity?"

**Agent responds:**

> "He said budget is $350K to $390K. He's pre-approved — has a letter from his lender. Needs to be in by August, his lease is up. Assign to Sarah. Best to reach him by text."

---

**Qualifier — completed lead object:**
```json
{
  "client_name": "Kevin Marsh",
  "contact_info": {
    "email": "kevin.marsh@gmail.com",
    "phone": "555-201-0187",
    "preferred_method": "text"
  },
  "intent": "buy",
  "status": "qualified",
  "stage": "qualifying",
  "lead_source": "zillow",
  "referral_from": null,
  "pre_approved": true,
  "budget_range": {
    "min": 350000,
    "max": 390000
  },
  "address_of_interest": "204 Creekside Dr, Millbrook",
  "timeline": "Must be in by August 2026 — lease expiring",
  "location_preferences": ["Cedarville", "Millbrook"],
  "constraints": {
    "tags": ["lease_expiring"],
    "notes": null
  },
  "assigned_agent": "Sarah",
  "recommended_next_action": "Research 204 Creekside Dr and send initial property report to Kevin via text",
  "created_date": "2026-05-12",
  "last_updated": "2026-05-12"
}
```

**Why `lease_expiring` was added:** Agent confirmed lease is up in August — this is an active constraint that affects timeline urgency and offer strategy.

**Why `first_time_buyer`, `must_sell_first`, `cash_buyer`, etc. were not asked:** The agent's response gave enough context to rule most of them out. Kevin has a lease (not a home to sell), has financing (not cash), no mention of investment intent. If any of these were unclear, the qualifier would have asked. When in doubt, ask rather than assume.

---

**contact_log update (Running Timeline entry):**
```
2026-05-12 — Lead Qualifier — Qualification complete. Kevin Marsh confirmed pre-approved with a budget of $350K–$390K, targeting Cedarville/Millbrook area, lease expiring August 2026. Assigned to Sarah. Ready for property research on 204 Creekside Dr.
```

**contact_log update (Current State overwrite):**
```
Current stage:     qualifying
Last action:       Lead qualification complete
Next action:       Research 204 Creekside Dr and send initial property report to Kevin via text
Open questions:    None
Deadline:          Lease expires August 2026 — needs to close before then
```

---

## Example 2: Seller Qualification

**Context:** The orchestrator received a call note from Diana about a potential listing client. The qualifier receives a partially populated lead object and completes it.

**Lead object as received:**
```json
{
  "client_name": "Patricia Odom",
  "contact_info": {
    "email": "patricia.odom@email.com",
    "phone": "555-301-0093",
    "preferred_method": null
  },
  "intent": "sell",
  "status": "pending_info",
  "stage": "qualifying",
  "lead_source": "referral",
  "referral_from": null,
  "pre_approved": null,
  "budget_range": {
    "min": null,
    "max": null
  },
  "asking_price_target": 520000,
  "address_of_interest": "812 Ridgewood Trail, Westbrook",
  "timeline": "90 days",
  "location_preferences": [],
  "constraints": {
    "tags": ["relocation"],
    "notes": null
  },
  "assigned_agent": "Diana",
  "recommended_next_action": "",
  "created_date": "2026-05-12",
  "last_updated": "2026-05-12"
}
```

**Fields already populated:** client_name, both contact fields, intent, lead_source, asking_price_target, address_of_interest, timeline, relocation tag, assigned_agent.

**Fields still needed:** referral_from (required since lead_source is "referral"), preferred_method, any additional constraint tags, recommended_next_action.

---

**Qualifier output — intake questions asked:**

> "Patricia came in as a referral — who sent her? I need the name for the referral record.
>
> Also:
> - How does she prefer to be contacted?
> - She's relocating — does she also need to buy somewhere else, or is she just selling?
> - Any idea of the mortgage situation on the property? Free and clear, or existing loan?"

**Agent responds:**

> "Referral from Tom Nguyen — he sold with us last year. She prefers email. She's moving to Denver, has family there, doesn't need us to help with the buy side. The house has a mortgage but plenty of equity — she's not worried about it. Oh, and it's an estate situation — the house was her mother's. She's the executor."

---

**Qualifier — completed lead object:**
```json
{
  "client_name": "Patricia Odom",
  "contact_info": {
    "email": "patricia.odom@email.com",
    "phone": "555-301-0093",
    "preferred_method": "email"
  },
  "intent": "sell",
  "status": "qualified",
  "stage": "qualifying",
  "lead_source": "referral",
  "referral_from": "Tom Nguyen",
  "pre_approved": null,
  "budget_range": {
    "min": null,
    "max": null
  },
  "asking_price_target": 520000,
  "address_of_interest": "812 Ridgewood Trail, Westbrook",
  "timeline": "90 days",
  "location_preferences": [],
  "constraints": {
    "tags": ["relocation", "estate_sale"],
    "notes": "Patricia is the executor of her mother's estate. Existing mortgage on property but sufficient equity — not a short sale. No buy-side needed; relocating to Denver independently."
  },
  "assigned_agent": "Diana",
  "recommended_next_action": "Run CMA on 812 Ridgewood Trail to compare against Patricia's $520K target before first contact call",
  "created_date": "2026-05-12",
  "last_updated": "2026-05-12"
}
```

**Why `estate_sale` was added:** Agent confirmed the property was inherited — Patricia is the executor. This is an active constraint that affects decision-making (she may need legal counsel before accepting offers) and communication tone.

**Why `must_sell_first` was not added:** Patricia does not need to buy simultaneously. The relocation is handled independently. No dependency exists.

**Why `pre_approved` and `budget_range` remain null:** Seller lead. These fields do not apply. They stay null.

---

**contact_log update (Running Timeline entry):**
```
2026-05-12 — Lead Qualifier — Qualification complete. Patricia Odom, referral from Tom Nguyen. Selling her late mother's estate at 812 Ridgewood Trail. Asking $520K. Relocating to Denver, no buy-side needed. Executor — may need legal coordination before offer acceptance. Assigned to Diana. CMA needed before first contact.
```

**contact_log update (Current State overwrite):**
```
Current stage:     qualifying
Last action:       Lead qualification complete
Next action:       Run CMA on 812 Ridgewood Trail and compare against $520K target
Open questions:    CMA will determine whether $520K is realistic — Diana should review before first call
Deadline:          90-day timeline to list
```

---

## What Happens Next (Both Examples)

The qualifier does not route. It returns the completed lead object and updated contact_log to the orchestrator. The orchestrator evaluates:

- Kevin (buyer): `status` = qualified, `budget_range.max` = 390000 → auto-chain to property research
- Patricia (seller): `status` = qualified, `address_of_interest` = non-null → auto-chain to property research for CMA
