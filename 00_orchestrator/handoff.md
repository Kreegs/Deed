# Orchestrator — Handoff

## What the Orchestrator Receives

The Orchestrator receives raw, unstructured input from the agent. It does not require the agent to format or categorize the input before pasting it. Common input types:

- A block of notes or a forwarded email about a new prospect
- A client reply pasted directly ("Susan emailed me back with this")
- A status question about an active deal ("Where are we with Marcus?")
- A stage update ("We just went under contract on the Chen deal")

The Orchestrator is responsible for determining what type of input it has received and what action follows.

---

## Context the Orchestrator Reads Before Acting

Before routing, the Orchestrator reads two sources:

**`active_cases.md`** — the index at the project root. Used to match a client name in the agent's input to the correct folder path, current stage, and assigned agent.

**`clients/open/[client_name]/contact_log.md`** — the deal's full history. Used to understand what has already happened, what is outstanding, and what the next action should be.

The agent does not need to attach files or specify a folder. The Orchestrator finds both automatically from the client name.

---

## What the Orchestrator Passes to Each Specialist

When invoking a specialist, the Orchestrator passes the following. These are not optional — if a required field is missing, the Orchestrator resolves it before routing.

**→ 01_lead_qualifier**
```
raw_lead_info:       [the unstructured notes or email the agent provided]
assigned_agent:      [name of the agent who owns this lead, if known]
```

**→ 02_property_research**
```
client_name:
intent:              [buy or sell — determines property report vs CMA]
address_of_interest: [from the lead object — for buyers, the property being researched; for sellers, the listing address]
property_folder:     clients/open/[client_name]/properties/[address_slug]/  (buyers only)
lead_object_path:    clients/open/[client_name]/lead_object.json
contact_log_path:    clients/open/[client_name]/contact_log.md
```

**→ 03_client_communication**
```
client_name:
intent:              [buy or sell — determines which template set applies]
communication_type:  [first_contact / property_info / offer_submitted / counter_offer_response / under_contract_intro /
                      closing_prep / congratulations_closing / listing_agreement_intro / showing_feedback_summary /
                      offer_received_summary / price_reduction_discussion / inspection_response / closing_delay /
                      general_update / cold_lead_follow_up]
assigned_agent:      [name — determines which voice profile to use]
deal_stage:          [current stage from lead_object.json]
contact_preference:  [from lead_object.contact_info.preferred_method]
context:             [relevant excerpt from contact_log or deal update triggering this communication]
contact_log_path:    clients/open/[client_name]/contact_log.md
```

**→ 04_transaction_coordinator**
```
client_name:
intent:              [buy or sell — determines which due diligence checklist applies]
contract_date:       [from lead_object.json, if set]
close_date:          [from lead_object.json, if known]
lead_object_path:    clients/open/[client_name]/lead_object.json
contact_log_path:    clients/open/[client_name]/contact_log.md
```

---

## What the Orchestrator Receives Back from Each Specialist

The Orchestrator reads each specialist's output to determine whether to chain automatically or pause at a gate.

**← 01_lead_qualifier** returns a completed `lead_object.json`. The Orchestrator writes the qualifier's updates back to `clients/open/[client_name]/lead_object.json` and evaluates routing conditions based on `intent`:

**Buyer auto-chain to property research:**
- `status` is `"qualified"`
- `budget_range.max` is non-null

**Seller auto-chain to property research (CMA):**
- `status` is `"qualified"`
- `address_of_interest` is non-null

If the unlock condition is missing, the Orchestrator holds and surfaces the missing field to the agent. It does not chain.

**← 02_property_research** writes its report to `clients/open/[client_name]/properties/[address_slug]/property_report.md` (buyers) or `clients/open/[client_name]/cma_report.md` (sellers) and sets `research_complete: true` in the relevant object. The Orchestrator confirms the file was written, then pauses: *"Research complete for [address]. Do you want to draft a first contact email?"*

**← 03_client_communication** writes the draft to `clients/open/[client_name]/email_drafts/` and updates the contact_log. The Orchestrator surfaces the draft and pauses: *"Do you want to send this?"* No email is sent without confirmation.

**← 04_transaction_coordinator** writes a deadline map, document checklist, and 48-hour action list to the client folder and updates the contact_log. If the TC flags a risk or blocker, the Orchestrator pauses: *"This needs your attention before we proceed."*

---

## What the Orchestrator Produces

**New client folder** — created at the start of a new lead, before any specialist is invoked:
```
clients/open/[client_name]/
├── contact_log.md            (seeded with Deal Identity section)
├── lead_object.json          (copied from template; populated by qualifier)
├── email_drafts/
├── cma_report.md             (sellers only — written by property research)
└── properties/               (buyers only)
    └── [address_slug]/
        ├── property_object.json   (copied from template on folder creation)
        └── property_report.md    (written by property research)
```

**active_cases.md updates** — the Orchestrator is the only specialist that writes to this file:
- Adds a row when a new lead is qualified
- Updates the Stage column at each specialist handoff
- Updates the Last Contact column at the end of each session
- Removes the row when a deal closes or a lead goes cold

**client_registry.md updates:**
- Adds a row when a new lead is qualified (not when first detected)
- Updates Closed Date and Folder columns when a deal closes or lead goes cold

**Closing sequence** — when a deal closes:
1. Verify all applicable `due_diligence` flags are `true` (hard gate)
2. Set `stage` → `"closed"` in lead_object.json
3. Route to `03_client_communication` for `congratulations_closing` message
4. Move `clients/open/[client_name]/` to `clients/closed/[client_name]/` via bash command
5. Remove the client's row from `active_cases.md`
6. Update `client_registry.md` with close date and new folder path
The folder is never deleted, only archived.
