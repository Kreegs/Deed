# Lead Qualifier — Handoff

## What the Lead Qualifier Receives

The orchestrator routes to the lead qualifier after pre-processing a new lead. The qualifier always receives:

**A partially populated `lead_object.json`** at `clients/open/[client_name]/lead_object.json`. The orchestrator has already extracted and populated whatever fields it could from the agent's raw input (name, email, phone, address, stated preferences, lead source). Fields the orchestrator could not determine are null or empty.

**Context from the agent's input** — the raw notes or email that triggered the intake, available for the qualifier to reference when asking follow-up questions.

The qualifier reads the lead object first. It does not ask for anything already populated.

---

## What the Lead Qualifier Produces

### 1. Updated `lead_object.json`

The qualifier writes its completed fields directly to the lead object file. It does not reconstruct or replace the file — it updates only the fields it owns. The orchestrator reads the updated file to evaluate routing conditions.

**Fields the qualifier owns and must resolve:**

```
contact_info.preferred_method    "email" | "phone" | "text"
lead_source                      enum (see below)
referral_from                    string — required if lead_source is "referral", null otherwise
pre_approved                     true | false | "pending" — buyers only; null for sellers
budget_range.min                 number | null — buyers only
budget_range.max                 number | null — buyers only; required to unlock property research
asking_price_target              number | null — sellers only
timeline                         string
location_preferences             array of strings
constraints.tags                 array of confirmed active constraint tags
constraints.notes                string | null — free text for anything not covered by a tag
assigned_agent                   string — confirm or set
recommended_next_action          string — required to close as "qualified"
status                           "qualified" | "pending_info" | "cold"
last_updated                     set to today's date
```

**`lead_source` valid values:** `"zillow"`, `"realtor_com"`, `"referral"`, `"open_house"`, `"website"`, `"sign_call"`, `"cold_call"`, `"repeat_client"`, `"other"`

**Fields the qualifier does not touch:**
- `stage` — orchestrator only
- `created_date` — set by orchestrator at folder creation
- `due_diligence` — set by transaction coordinator at contract stage
- `contract_date`, `close_date` — set by transaction coordinator

### 2. Updated `contact_log.md`

The qualifier appends one entry to the **Running Timeline** section:
```
[date] — Lead Qualifier — Qualification [complete / incomplete]. [1–2 sentence summary of what was confirmed and what, if anything, remains open.]
```

The qualifier overwrites the **Current State** section:
```
Current stage:       qualifying
Last action:         Lead qualification [complete / incomplete]
Next action:         [value of recommended_next_action]
Open questions:      [any fields still missing that would affect routing, or "None"]
Deadline:            [if timeline creates urgency, note it here; otherwise "None identified"]
```

---

## Routing Conditions the Orchestrator Evaluates After Qualification

The qualifier does not route. It produces a completed lead object and updated contact_log, then returns control to the orchestrator. The orchestrator evaluates these conditions **immediately and automatically — it does not ask the agent what to do next first.**

**If `status` is `"qualified"` and `intent` is `"buy"`:**
- If `budget_range.max` is non-null → auto-chain to `02_property_research` now
- If `budget_range.max` is null → hold; surface missing field to agent

**If `status` is `"qualified"` and `intent` is `"sell"`:**
- If `address_of_interest` is non-null → auto-chain to `02_property_research` for a CMA now
- If `address_of_interest` is null → hold; surface missing field to agent

**Important:** For sellers, the CMA runs before any email is drafted. The orchestrator does not offer to draft a first contact email at this point — that gate comes after research completes, not after qualification.

**If `status` is `"pending_info"`:**
- Orchestrator holds. It does not chain. It surfaces what is missing so the agent can follow up with the client.

**If `status` is `"cold"`:**
- Orchestrator does not chain. It notes the cold status in the contact_log, removes the client from `active_cases.md` if they were listed, moves the folder to `clients/closed/`, and updates `client_registry.md`.

---

## Minimum Fields Required to Set `status: "qualified"`

| Field | Requirement |
|---|---|
| `client_name` | Non-empty string |
| `contact_info.email` or `contact_info.phone` | At least one must be non-null |
| `intent` | `"buy"` or `"sell"` |
| `recommended_next_action` | Non-empty string |

All four must be present. If any are missing, `status` stays `"pending_info"`.
