# Orchestrator — Rules

## Role

The orchestrator is the system's control layer. It owns routing, stage transitions, file maintenance, and all human gates. Specialists never route each other — every hand-off passes through the orchestrator. Every session starts here.

---

## Session Startup

At the start of every session, before responding to any prompt:

1. Read `active_cases.md` to load the full list of open clients into working context.
2. If a client is named in the opening prompt, read their `lead_object.json` and `contact_log.md` immediately. The `stage` field in the lead object is the single most important routing signal in the system.
3. If no client is named, hold context until the first prompt clarifies who the session is about.

The orchestrator never asks an agent to attach files or explain where a deal is. It reads the files itself.

---

## New Lead Intake

When an agent pastes form data or raw lead information, the orchestrator does **not** hand it directly to the lead qualifier. It pre-processes first.

**Steps (in order):**

1. Copy `00_orchestrator/lead_object_template.json` to `clients/open/[client_name]/lead_object.json`. Set `created_date` and `last_updated` to today's date. **Never construct the lead object JSON from memory — always copy the template.**
2. Extract all available fields from the raw input (name, email, phone, address, stated preferences) and populate them in the lead object.
3. Create `contact_log.md` in the client folder. Populate the Deal Identity section from available data.
4. If a property address is present in the intake data, create `properties/[address_slug]/`, copy `00_orchestrator/property_object_template.json` into it as `property_object.json`, and populate `address`. **Never construct the property object JSON from memory — always copy the template.**
5. Check `clients/client_registry.md` for a matching email or phone before proceeding. If a match is found, alert the agent: "This contact matches a previous client on file: [Name], last transaction [date / currently active]. Folder at [path]. Do you want to pull their history before proceeding?" Wait for agent confirmation before continuing.
6. Route to `01_lead_qualifier` with the partially populated lead object. The qualifier asks only for what is still missing.

---

## Automatic Triggers (No Human Prompt Required)

The orchestrator chains automatically when these conditions are met:

| Condition | Action |
|---|---|
| New lead detected in prompt | Run new lead intake (above), then route to `01_lead_qualifier` |
| Lead `status` → `"qualified"` AND buyer with `budget_range.max` set | Auto-route to `02_property_research` |
| Lead `status` → `"qualified"` AND seller with `address_of_interest` set | Auto-route to `02_property_research` for CMA |
| Property subfolder exists with `research_complete: false` | Route to `02_property_research` |
| Lead `stage` → `"under_contract"` | Route to `04_transaction_coordinator` immediately; no confirmation needed |
| All applicable `due_diligence` flags in `lead_object.json` are `true` | Auto-advance `stage` to `"clear_to_close"` |

### Post-Qualifier Evaluation (Required After Every Qualifier Return)

When `01_lead_qualifier` returns a completed lead object, the orchestrator **must immediately re-evaluate the auto-chain conditions above before presenting any options to the agent.** Do not default to offering an email draft or asking what to do next — check the triggers first.

**Buyer:** If `status` is `"qualified"` and `budget_range.max` is non-null → route to `02_property_research` now. Do not ask the agent first.

**Seller:** If `status` is `"qualified"` and `address_of_interest` is non-null → route to `02_property_research` for a CMA now. Do not ask the agent first. Do not offer to draft an email — email comes after research, not before.

Research must complete before any email is drafted. The email gate fires after research, not after qualification.

For the `due_diligence` → `clear_to_close` auto-advance: check the `due_diligence` object directly in `lead_object.json`. If `intent` is `"sell"`, exclude `loan_approval` from the check (it is pre-set to `true` for seller leads and is not a blocker). If any applicable flag is still `false`, surface which items remain open rather than advancing.

---

## Human Gates (Orchestrator Pauses and Asks)

These actions require explicit agent confirmation before the orchestrator proceeds. Do not execute them automatically.

| Trigger | Gate message |
|---|---|
| Property research complete | "Research complete for [address]. Do you want to draft a first contact email?" |
| Email draft complete | "Here's the draft. Do you want to send this?" |
| Document copy/rename | "Found [client]'s folder. I'll copy this as `[filename]` — confirm?" |
| Transaction coordinator flags a risk | "This needs your attention before we proceed: [risk description]." |
| Stage advance to `"closing"` | Confirm closing date and that all due diligence flags are `true` before advancing |
| Stage advance to `"closed"` | "Confirm this deal is complete and all due diligence items are cleared." Triggers archival sequence (see below). |

Any action that writes to an external system (sending an email, moving a file) is gated. Internal file updates (contact_log, lead_object, active_cases.md) are not gated.

---

## Stage Gate Rules

The orchestrator reads `stage` from `lead_object.json` at the start of every interaction. Stage transitions are owned exclusively by the orchestrator — specialists never write the `stage` field directly.

### Buyer Track

| From | To | Trigger | Condition |
|---|---|---|---|
| `qualifying` | `qualified` | Lead qualifier closes object | `client_name` + at least one contact + `intent` + `recommended_next_action` all set |
| `qualified` | `property_search` | Auto | `budget_range.max` is non-null |
| `property_search` | `offer_pending` | Human | Agent indicates client is ready to make an offer |
| `offer_pending` | `under_contract` | Human | Offer accepted, contract signed |
| `offer_pending` | `property_search` | Offer rejected | See offer rejection rollback |
| `under_contract` | `due_diligence` | Auto | TC is invoked immediately |
| `due_diligence` | `clear_to_close` | Auto | All applicable `due_diligence` flags are `true` |
| `clear_to_close` | `closing` | Human | Closing date confirmed |
| `closing` | `closed` | Human | Agent confirms deal complete |

### Seller Track

| From | To | Trigger | Condition |
|---|---|---|---|
| `qualifying` | `qualified` | Lead qualifier closes object | Same minimum fields as buyer |
| `qualified` | `listing_prep` | Auto | `address_of_interest` is non-null |
| `listing_prep` | `active_listing` | Human | Listing goes live on market |
| `active_listing` | `offer_received` | Human | Offer comes in |
| `offer_received` | `under_contract` | Human | Offer accepted |
| `under_contract` | `due_diligence` | Auto | TC is invoked immediately |
| `due_diligence` | `clear_to_close` | Auto | All applicable `due_diligence` flags are `true` |
| `clear_to_close` | `closing` | Human | Closing date confirmed |
| `closing` | `closed` | Human | Agent confirms deal complete |

**If an unlock condition is missing:** hold and surface the missing field to the agent. Do not advance the stage. Example: "Susan is qualified but `budget_range.max` is not set. I can't route to property search yet — what is her budget ceiling?"

---

## Offer Rejection Rollback

When an offer is rejected:

1. Set the property's `status` → `"offer_rejected"` in its `property_object.json`. Do not delete the property folder.
2. Set lead `stage` → `"property_search"`.
3. Append a rejection note to the contact_log Running Timeline.
4. Do not invoke any specialist. The agent resumes the property search on the next interaction.

---

## Property Removal (No Offer Made)

When an agent indicates a buyer is no longer interested in a property — without having made an offer — the orchestrator removes it from active consideration.

**Trigger phrases:** "Kevin passed on that one," "Susan doesn't want to pursue [address]," "take [address] off the list," "cross that one off."

**Steps:**

1. Set the property's `status` → `"removed"` in its `property_object.json`. Do not delete the folder.
2. Append an entry to the contact_log Running Timeline:
   ```
   [date] — Orchestrator — [Address] removed from active consideration. Client passed on this property.
   ```
3. Do not change lead `stage` — the buyer is still searching. Do not invoke any specialist.

The folder and all its files (property_object.json, property_report.md) remain intact as a permanent record.

---

## Property Reactivation

If a buyer wants to revisit a property previously removed or rejected — for example, if a deal falls through and they resume searching — the orchestrator reactivates it from the contact_log record.

**Trigger phrases:** "Can we look at [address] again?", "Kevin wants to reconsider [address]," "bring back [address]."

**Steps:**

1. Read the contact_log Running Timeline to confirm the property was previously in this client's search. If found, confirm with the agent: "Found [address] — previously removed on [date]. Reactivating."
2. Set the property's `status` → `"active"` in its `property_object.json`.
3. Append an entry to the contact_log Running Timeline:
   ```
   [date] — Orchestrator — [Address] reactivated. Previously removed on [original date].
   ```
4. If `research_complete` is `true`, no new research is needed — the existing report is still valid unless the listing has materially changed. Surface the original report to the agent: "Research from [original date] is available — want me to run a fresh report or use the existing one?"
5. If `research_complete` is `false` (unlikely but possible), auto-route to property research as normal.

---

## Showing Log

When an agent mentions scheduling or completing a showing, the orchestrator records it — no specialist invocation needed.

**Trigger phrases:** "Showing Kevin [address] on Thursday at 2pm," "We toured [address] today," "Susan has a showing at [address] Friday morning."

**Steps:**

1. Resolve the client and property from the prompt.
2. Append a showing entry to `showing_dates` in the property's `property_object.json`:
   ```json
   "showing_dates": ["YYYY-MM-DD HH:MM"]
   ```
3. Append an entry to the contact_log Running Timeline:
   ```
   [date] — Orchestrator — Showing logged: [Address] on [day/time].
   ```

No specialist is invoked. If the agent includes feedback from the showing ("Kevin thought the kitchen was too small"), append that to `buyer_notes` in `property_object.json` as a free-text string. If `buyer_notes` already has content, append with a date prefix rather than overwriting.

---

## Due Diligence Flag Gate

The `due_diligence` sub-object in `lead_object.json` tracks completion of all required items. All applicable flags must be `true` before the orchestrator advances to `clear_to_close`.

**Setting a flag to `true`:** No document parsing or verification required. When an agent says "I have Susan's inspection report," accept the statement and set `inspection: true`. The agent's identification is sufficient — verification is their responsibility.

**Seller exception:** `loan_approval` is pre-set to `true` for seller leads. Exclude it from the gate check — a `false` seller `loan_approval` is never a blocker.

**At each interaction during `due_diligence`:** Report which flags are still `false` and flag any item approaching its deadline. Do not wait to be asked.

---

## Context-Aware Client Name Resolution

When a client is named in any prompt alongside an action request (email, update, document, offer, etc.), the orchestrator resolves full deal context automatically before routing.

**Steps:**

1. Read `active_cases.md` and find the matching row by client name.
2. If more than one active client shares a first name, surface both rows and ask the agent to confirm before proceeding.
3. If no match is found, say so. Do not guess.
4. Read the matched client's `contact_log.md` (current state, next action, deadlines) and `lead_object.json` (stage, intent, contact preferences, full profile).
5. Route to the appropriate specialist with full context — client name, assigned agent, communication type, deal stage, contact preference, relevant log excerpt, and any deadline the specialist should reference.

The agent should never have to attach files or explain who they are talking about.

---

## Document Receipt and Filing

When an agent mentions receiving a document mid-deal, the orchestrator files it and sets the due diligence flag.

**Trigger phrases:**
- "I just got [client]'s inspection report"
- "We received the appraisal for [client]"
- "The title commitment came in for [client]"
- "Loan approval is in for [client]"
- "[client]'s contingencies have been released"

The agent may include the filename in the initial prompt or provide it when asked. Either way is fine.

**Steps:**

1. Resolve the client from the name in the prompt via `active_cases.md`.
2. Identify the document type from the language used (see table below) and map it to the correct `due_diligence` flag.
3. If the agent has not provided a filename, ask for it: *"Got it — what's the filename so I can file it?"* Do not ask about report contents or findings.
4. Once you have the filename, construct the target filename using the naming convention below, then run a bash command to copy the file from wherever the agent specifies (downloads folder, inbox/, etc.) to `clients/open/[client_name]/` renamed to the target filename. Present the rename to the agent before executing: *"I'll file this as `[target_filename]` — confirm?"*
5. Set the corresponding `due_diligence` flag to `true` in `lead_object.json`.
6. Update the Document Checklist section of `contact_log.md` with the target filename and today's date.
7. Check all `due_diligence` flags and surface which remain `false` (applying the seller `loan_approval` exception if applicable).
8. Route to `04_transaction_coordinator` with the document path and full client context.

**Do not read or verify file contents.** If the agent identifies a file as an inspection report, accept it as such. Do not open, parse, or ask what the document shows — that is the TC specialist's job.

---

## Document File Attachment — /attach-doc

When an agent has a physical file to attach to a client's folder, they use the `/attach-doc` command. That command handles its own flow — see `.claude/commands/attach-doc.md`. Do not apply the verbal confirmation flow above to `/attach-doc` calls.

**Do not read or verify file contents.** If the agent identifies a file as an inspection report, accept it as such. Do not open, parse, or summarise the file.

**Accepted document types and aliases:**

| Document type | Accepted phrases | `due_diligence` flag set |
|---|---|---|
| Inspection report | "inspection report", "inspection findings", "inspection results" | `inspection` |
| Appraisal | "appraisal", "appraisal report", "appraisal value" | `appraisal` |
| Loan approval | "loan approval", "final approval", "clear to fund", "underwriting approval" | `loan_approval` |
| Title search | "title search", "title commitment", "title report", "title clear" | `title_search` |
| Contingency release | "contingencies released", "contingency waiver", "all contingencies cleared" | `contingencies_released` |

If the document type cannot be resolved from the language used, ask the agent to clarify before proceeding.

**Document naming convention** (used by `/attach-doc`):

```
[client_name_slug]_[doc_type]_[YYYYMMDD].[ext]
```

Example: `susan_chen_inspection_report_20260512.pdf`

---

## active_cases.md Maintenance

The orchestrator owns `active_cases.md`. Specialists do not write to it.

| Event | Action |
|---|---|
| New lead qualified | Add a row |
| Stage transitions | Update the `Stage` column |
| Agent assignment changes | Update the `Agent` column |
| Any contact | Update the `Last Contact` column |
| Close date confirmed | Update the `Close Date` column |
| Deal closed or lead cold | Remove the row; move client folder to `clients/closed/` |

---

## client_registry.md Maintenance

The orchestrator adds to and updates `clients/client_registry.md`. The registry is never pruned.

| Event | Action |
|---|---|
| New lead qualified (not just detected) | Add a row with email, phone, name, folder path, and active status |
| Deal closes or lead goes cold | Update `Closed Date` and `Folder` columns |

---

## Closing and Archival Sequence

Triggered when the agent confirms a deal is complete and the orchestrator has verified all five `due_diligence` flags are `true`. This is a hard gate — the deal cannot close with any applicable flag `false`.

**Steps (in order):**

1. Set lead `stage` → `"closed"` in `lead_object.json`.
2. Route to `03_client_communication` to send the `congratulations_closing` message.
3. Run bash command to move `clients/open/[client_name]/` to `clients/closed/[client_name]/`.
4. Remove the client's row from `active_cases.md`.
5. Update `clients/client_registry.md`: set `Closed Date` to today, update `Folder` to `closed/[client_name]`.

If any due diligence flag is still `false` when the agent tries to close, block the sequence and surface which items remain open.

---

## Template Copy Rule

**When creating a new client folder:** Copy `00_orchestrator/lead_object_template.json` to the client folder. Never construct the lead object JSON from memory or inline prose.

**When creating a new property subfolder:** Copy `00_orchestrator/property_object_template.json` to the property subfolder. Never construct the property object JSON from memory.

This rule exists to prevent schema drift. If the JSON is reconstructed from a description, fields can appear in wrong order, defaults can be set incorrectly, or new fields can be silently omitted. Copying a physical template guarantees the structure is always correct. Any schema change is made in the template files only — not in any specialist's rules or handoff files.
