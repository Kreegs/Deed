# Orchestrator — Examples

Two worked examples: one buyer, one seller. Each shows the full orchestrator decision path from raw agent input to specialist invocation.

---

## Example 1: New Buyer Lead (Full Intake to Property Research)

**Input from agent:**

> "Got a new lead from Zillow. Name is Kevin Marsh, email kevin.marsh@gmail.com, phone 555-201-0187. He's looking to buy in Cedarville or Millbrook, budget around $380K. Says he's pre-approved. Interested in 204 Creekside Dr, Millbrook."

---

**Orchestrator decision path:**

**Step 1 — Identify input type.**
Raw lead pasted by agent. Not a client reply, not a status question. Trigger: new lead intake.

**Step 2 — Check client registry.**
Search `clients/client_registry.md` for kevin.marsh@gmail.com and 555-201-0187. No match found. Proceed.

**Step 3 — Create client folder and copy templates.**
```
clients/open/kevin_marsh/
├── contact_log.md           ← created, Deal Identity populated
├── lead_object.json         ← copied from 00_orchestrator/lead_object_template.json
└── properties/
    └── 204_creekside_dr/
        └── property_object.json  ← copied from 00_orchestrator/property_object_template.json
```
Set `created_date` and `last_updated` to today in lead_object.json.

**Step 4 — Pre-populate lead object from input.**
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
  "pre_approved": true,
  "budget_range": {
    "min": null,
    "max": 380000
  },
  "address_of_interest": "204 Creekside Dr, Millbrook",
  "location_preferences": ["Cedarville", "Millbrook"]
}
```

**Step 5 — Route to 01_lead_qualifier.**
Pass the partially populated lead object. The qualifier asks only for what is still missing: preferred contact method, timeline, constraint tags (must-sell-first, investment property, etc.), and budget floor if relevant.

**Step 6 — Lead qualifier returns status: "qualified".**
Qualifier confirms all required fields are present. Updates `status` → `"qualified"`, `stage` → `"property_search"`, sets `recommended_next_action`.

**Step 7 — Evaluate auto-chain conditions.**
- `status` = `"qualified"` ✓
- `budget_range.max` = 380000 (non-null) ✓
- `properties/204_creekside_dr/` exists with `research_complete: false` ✓

Auto-chain to `02_property_research`. No human prompt needed.

**Step 8 — Route to 02_property_research.**
```
client_name:         Kevin Marsh
intent:              buy
address_of_interest: 204 Creekside Dr, Millbrook
property_folder:     clients/open/kevin_marsh/properties/204_creekside_dr/
lead_object_path:    clients/open/kevin_marsh/lead_object.json
contact_log_path:    clients/open/kevin_marsh/contact_log.md
```

**Step 9 — Property research completes.**
Specialist writes `property_report.md` to `clients/open/kevin_marsh/properties/204_creekside_dr/`, sets `research_complete: true` in `property_object.json`, and updates contact_log.

**Step 10 — Human gate.**
Orchestrator pauses: *"Research complete for 204 Creekside Dr. Do you want to draft a first contact email?"*

**Step 11 — Agent confirms.**
Route to `03_client_communication` with `communication_type: first_contact`, `assigned_agent: [agent name]`, and property report context.

**Step 12 — Email draft complete.**
Orchestrator surfaces the draft: *"Here's the draft. Do you want to send this?"* Waits for confirmation before any external action.

**Step 13 — active_cases.md and client_registry.md updated.**
```
| Kevin Marsh | kevin_marsh | Buy | Property Search | Sarah | 05/12/2026 | — |
```

---

## Example 2: New Seller Lead (Intake to CMA)

**Input from agent:**

> "Diana has a potential listing. Client is Patricia Odom, patricia.odom@email.com, 555-301-0093. She owns a home at 812 Ridgewood Trail, Westbrook. Wants to list at $520K but isn't sure if that's realistic. Needs to sell because she's relocating to Denver. Timeline is 90 days. Assign to Diana."

---

**Orchestrator decision path:**

**Step 1 — Identify input type.**
Raw lead pasted by agent. Trigger: new lead intake.

**Step 2 — Check client registry.**
Search `clients/client_registry.md` for patricia.odom@email.com and 555-301-0093. No match found. Proceed.

**Step 3 — Create client folder and copy template.**
```
clients/open/patricia_odom/
├── contact_log.md       ← created, Deal Identity populated
└── lead_object.json     ← copied from 00_orchestrator/lead_object_template.json
```
No `properties/` directory for sellers — the listing address lives in `address_of_interest`.

**Step 4 — Pre-populate lead object from input.**
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
  "assigned_agent": "Diana",
  "address_of_interest": "812 Ridgewood Trail, Westbrook",
  "asking_price_target": 520000,
  "timeline": "90 days",
  "constraints": {
    "tags": ["relocation"],
    "notes": null
  }
}
```

**Step 5 — Route to 01_lead_qualifier.**
Pass the partially populated lead object. The qualifier asks for what is missing: preferred contact method, current mortgage status, whether she also needs to buy in Denver (would add `must_sell_first` if timing-dependent), any additional seller constraints.

**Step 6 — Lead qualifier returns status: "qualified".**
All required fields confirmed. `status` → `"qualified"`, `stage` → `"listing_prep"`, `recommended_next_action` set.

**Step 7 — Evaluate auto-chain conditions.**
- `status` = `"qualified"` ✓
- `intent` = `"sell"` → check for `address_of_interest`
- `address_of_interest` = `"812 Ridgewood Trail, Westbrook"` (non-null) ✓

Auto-chain to `02_property_research` for a CMA. No human prompt needed.

**Step 8 — Route to 02_property_research.**
```
client_name:         Patricia Odom
intent:              sell
address_of_interest: 812 Ridgewood Trail, Westbrook
lead_object_path:    clients/open/patricia_odom/lead_object.json
contact_log_path:    clients/open/patricia_odom/contact_log.md
```

**Step 9 — Property research completes.**
Specialist writes `cma_report.md` to `clients/open/patricia_odom/`, comparing recent comparable sales against Patricia's `asking_price_target` of $520K. Updates contact_log.

**Step 10 — Human gate.**
Orchestrator pauses: *"CMA complete for 812 Ridgewood Trail. Comparable sales suggest a realistic range of $490K–$510K against Patricia's target of $520K. Do you want to draft a first contact email?"*

**Step 11 — Agent confirms.**
Route to `03_client_communication` with `communication_type: first_contact`, `assigned_agent: Diana`, and CMA context.

**Step 12 — Email draft complete.**
Orchestrator surfaces the draft: *"Here's the draft in Diana's voice. Do you want to send this?"*

**Step 13 — active_cases.md and client_registry.md updated.**
```
| Patricia Odom | patricia_odom | Sell | Listing Prep | Diana | 05/12/2026 | — |
```

---

## Example 3: Mid-Deal Document Upload

**Input from agent (mid-session, deal already under contract):**

> "I just got Marcus's inspection report."

---

**Orchestrator decision path:**

**Step 1 — Resolve client.**
Read `active_cases.md`. Find Marcus Webb, folder `marcus_webb`, stage `due_diligence`, assigned agent Diana.

**Step 2 — Identify document type.**
"inspection report" → maps to `due_diligence.inspection` flag. Specialist: `04_transaction_coordinator`.

**Step 3 — Human gate (file operation).**
*"Found Marcus Webb's folder. I'll copy this as `marcus_webb_inspection_report_20260512.pdf` — confirm?"*

**Step 4 — Agent confirms.**
Run bash command: copy file from downloads to `clients/open/marcus_webb/`, rename per convention.

**Step 5 — Update lead object.**
Set `due_diligence.inspection` → `true` in `clients/open/marcus_webb/lead_object.json`.

**Step 6 — Update contact_log.**
Append to Document Checklist: `marcus_webb_inspection_report_20260512.pdf — received 2026-05-12`.

**Step 7 — Check due diligence gate.**
Read all `due_diligence` flags. `loan_approval`, `title_search`, and `contingencies_released` are still `false`. Do not advance stage. Surface remaining items: *"Inspection received. Still outstanding: loan approval, title search, contingencies released."*

**Step 8 — Route to 04_transaction_coordinator.**
Pass document path and full client context. TC reviews the report and flags any items requiring action (repair request decision, credit negotiation, etc.).
