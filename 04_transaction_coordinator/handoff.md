# Transaction Coordinator — Handoff

## What the Transaction Coordinator Receives

The orchestrator routes here in two situations: initial activation at `under_contract`, and any subsequent interaction while `stage` is `due_diligence`.

### Initial Activation

```
client_name:         [string]
intent:              "buy" | "sell"
contract_date:       [ISO date — YYYY-MM-DD — date contract was signed]
close_date:          [ISO date — YYYY-MM-DD — agreed closing date, if confirmed]
lead_object_path:    clients/open/[client_name]/lead_object.json
contact_log_path:    clients/open/[client_name]/contact_log.md
```

If `close_date` is null (not yet confirmed), the TC flags this immediately and asks the agent to confirm before generating the deadline map. Every deadline in the system depends on this date.

### Subsequent Interactions During Due Diligence

```
client_name:         [string]
intent:              "buy" | "sell"
trigger:             [what prompted this interaction — e.g., "document received", "agent check-in",
                      "flag update request", "close_date change"]
document_path:       [path to received document, if applicable — null otherwise]
lead_object_path:    clients/open/[client_name]/lead_object.json
contact_log_path:    clients/open/[client_name]/contact_log.md
```

The TC always reads the current `due_diligence` object and Document Checklist from the files directly — it does not rely on the trigger message to know the current state.

---

## What the Transaction Coordinator Produces

### On Initial Activation

**1. Updated `lead_object.json`** — the TC writes:
```json
{
  "contract_date": "YYYY-MM-DD",
  "close_date": "YYYY-MM-DD",
  "last_updated": "YYYY-MM-DD",
  "due_diligence": {
    "inspection": false,
    "appraisal": false,
    "loan_approval": false,    ← true for seller leads
    "title_search": false,
    "contingencies_released": false
  }
}
```

**2. Document Checklist written to `contact_log.md`** (overwrites the section):

```
## Document Checklist

Generated: [date]  |  Contract date: [contract_date]  |  Close date: [close_date]

### Due Diligence Deadlines

| Item | Target Date | Status | Notes |
|---|---|---|---|
| [Buyer: Inspection scheduled]               | [date] | Pending | |
| [Buyer: Inspection report received]         | [date] | Pending | |
| [Buyer: Inspection response submitted]      | [date] | Pending | |
| [Buyer: Appraisal ordered]                  | [date] | Pending | |
| [Buyer: Appraisal received]                 | [date] | Pending | |
| [Buyer: Loan approval confirmed]            | [date] | Pending | |
| [Buyer: Title search complete]              | [date] | Pending | |
| [Buyer: Contingencies released]             | [date] | Pending | |

[Seller items use the seller timeline — see rules.md for the full seller table]

### Documents Received

| Document | File | Date Received |
|---|---|---|
| [none yet] | — | — |

### Clear-to-Close Checklist
[Generated when all due diligence items are complete]
```

**3. 48-hour action list** — a plain-language list of what needs to happen in the next two business days:

*Buyer example:*
```
Next 48 hours:
- Schedule inspection (target: within 3 days of contract_date)
- Confirm with your lender that they have received the executed contract and have ordered the appraisal
- Ensure the title company has been engaged (your agent is handling this if not already done)
```

*Seller example:*
```
Next 48 hours:
- Ensure all required seller disclosures are ready to deliver to the buyer's agent
- Confirm the property is accessible for the buyer's inspection — expect it to be scheduled within the next 3–5 days
- Confirm title company has been notified and has the executed contract
```

**4. Running Timeline entry in `contact_log.md`:**
```
[date] — Transaction Coordinator — Deal activated at under_contract. Contract date: [date].
Close date: [date]. Deadline map generated. Due diligence period begins. 48-hour action list
surfaced to agent.
```

---

### On Subsequent Interactions

**1. Due diligence status report** (format defined in rules.md) — surfaced to agent immediately.

**2. Updated Document Checklist** in `contact_log.md` — status column updated for any items confirmed complete, flagged, or newly at risk. Documents received table updated if a document was received.

**3. Running Timeline entry:**
```
[date] — Transaction Coordinator — Due diligence check-in. [X of Y] items complete.
[Specific flags remaining open]. [Any Urgent or Watch items called out.]
```

**4. If document received** — document review summary:
```
[Document type] reviewed. [Key finding in 1–2 sentences.]
[Action required / No action required at this time.]
[If inspection_response or other client communication is warranted, flag to orchestrator.]
```

---

## What the Orchestrator Does with the TC's Output

**After initial activation:** Confirms lead_object.json was updated, Document Checklist was written, and 48-hour action list was produced. Returns control to the agent — no auto-chain. The agent works their items.

**After each due diligence check-in:** If all applicable flags are `true`, the orchestrator advances `stage` to `"clear_to_close"` automatically and routes back to the TC to generate the closing checklist.

**If TC flags a risk or blocker:** Orchestrator pauses and surfaces it to the agent: "This needs your attention before we proceed: [risk description]."

**After clear-to-close checklist is generated:** Orchestrator surfaces the checklist to the agent. No auto-chain — the agent works through the final items.
