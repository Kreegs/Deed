# Transaction Coordinator — Examples

Three worked examples: initial activation for a buyer, a mid-due-diligence check-in with a risk flag, and initial activation for a seller.

---

## Example 1: Buyer Initial Activation (Kevin Marsh)

**Context:** Kevin Marsh's offer on 456 Oak Ave was accepted. Contract signed 2026-05-20. Close date agreed: 2026-06-19 (30-day close). Sarah is the assigned agent.

**Received from orchestrator:**
```
client_name:         Kevin Marsh
intent:              buy
contract_date:       2026-05-20
close_date:          2026-06-19
lead_object_path:    clients/open/kevin_marsh/lead_object.json
contact_log_path:    clients/open/kevin_marsh/contact_log.md
```

---

**Step 1 — Update `lead_object.json`:**
```json
{
  "contract_date": "2026-05-20",
  "close_date": "2026-06-19",
  "last_updated": "2026-05-20",
  "due_diligence": {
    "inspection": false,
    "appraisal": false,
    "loan_approval": false,
    "title_search": false,
    "contingencies_released": false
  }
}
```

**Step 2 — Generate deadline map (counting back from 2026-06-19):**

| Item | Target Date | Calculation |
|---|---|---|
| Inspection scheduled | 2026-05-23 | contract_date + 3 days |
| Inspection report received | 2026-05-27 | contract_date + 7 days |
| Inspection response submitted | 2026-05-30 | contract_date + 10 days |
| Appraisal ordered | 2026-05-25 | contract_date + 5 days |
| Loan approval confirmed | 2026-06-05 | close_date − 14 days |
| Appraisal received | 2026-06-05 | close_date − 14 days |
| Title search complete | 2026-06-09 | close_date − 10 days |
| Contingencies released | 2026-06-09 | close_date − 10 days |
| Closing disclosure received | 2026-06-16 | close_date − 3 days |
| Final walkthrough | 2026-06-18 | close_date − 1 day |

**Step 3 — Write Document Checklist to `contact_log.md`:**

```
## Document Checklist

Generated: 2026-05-20  |  Contract date: 2026-05-20  |  Close date: 2026-06-19

### Due Diligence Deadlines

| Item | Target Date | Status | Notes |
|---|---|---|---|
| Inspection scheduled | 2026-05-23 | Pending | |
| Inspection report received | 2026-05-27 | Pending | |
| Inspection response submitted | 2026-05-30 | Pending | |
| Appraisal ordered | 2026-05-25 | Pending | |
| Appraisal received | 2026-06-05 | Pending | |
| Loan approval confirmed | 2026-06-05 | Pending | |
| Title search complete | 2026-06-09 | Pending | |
| Contingencies released | 2026-06-09 | Pending | |
| Closing disclosure received | 2026-06-16 | Pending | |
| Final walkthrough | 2026-06-18 | Pending | |

### Documents Received

| Document | File | Date Received |
|---|---|---|
| (none yet) | — | — |

### Clear-to-Close Checklist
[Generated when all due diligence items are complete]
```

**Step 4 — 48-hour action list surfaced to agent:**
```
Next 48 hours for Kevin Marsh:
- Schedule inspection now — target date is 2026-05-23 (3 days away)
- Confirm with Kevin's lender that they have the executed contract and are ordering the appraisal
- Ensure title company has been notified and has the contract on file
```

**Step 5 — Running Timeline entry:**
```
2026-05-20 — Transaction Coordinator — Deal activated at under_contract. Contract date:
2026-05-20. Close date: 2026-06-19. Deadline map generated for 30-day close. Inspection
must be scheduled by 2026-05-23. Sarah notified of 48-hour action items.
```

---

## Example 2: Mid-Due-Diligence Check-In with Risk Flag (Kevin Marsh, Day 12)

**Context:** It is 2026-06-01. Eleven days have passed since contract. The agent checks in asking for a status update.

**TC reads `lead_object.json` — current flag state:**
```json
"due_diligence": {
  "inspection": true,
  "appraisal": false,
  "loan_approval": false,
  "title_search": false,
  "contingencies_released": false
}
```

**TC reads Document Checklist — calculates status against today (2026-06-01):**

| Item | Target Date | Days Remaining | Status |
|---|---|---|---|
| Inspection scheduled | 2026-05-23 | — | ✓ Complete |
| Inspection report received | 2026-05-27 | — | ✓ Complete |
| Inspection response submitted | 2026-05-30 | — | ✓ Complete |
| Appraisal ordered | 2026-05-25 | — | ✓ Complete (confirmed ordered) |
| Appraisal received | 2026-06-05 | 4 days | Watch |
| Loan approval confirmed | 2026-06-05 | 4 days | Watch |
| Title search complete | 2026-06-09 | 8 days | Pending |
| Contingencies released | 2026-06-09 | 8 days | Pending |
| Closing disclosure received | 2026-06-16 | 15 days | Pending |
| Final walkthrough | 2026-06-18 | 17 days | Pending |

**Status report surfaced to agent:**

```
Due Diligence Status — Kevin Marsh — 2026-06-01
Close date: 2026-06-19 (18 days away)

✓ Complete:
  - Inspection — completed, response submitted as-is (2026-05-30)

⚠ Watch (due within 4 days):
  - Appraisal received — due 2026-06-05 (4 days)
    Recommended action: Confirm with lender that appraisal is scheduled and on track.
    If not yet scheduled, it needs to happen today.
  - Loan approval confirmed — due 2026-06-05 (4 days)
    Recommended action: Check with Kevin's lender on underwriting status.
    Final approval is needed by the same date as appraisal.

⬜ Pending (on track):
  - Title search complete — due 2026-06-09 (8 days)
  - Contingencies released — due 2026-06-09 (8 days)
  - Closing disclosure received — due 2026-06-16 (15 days)
  - Final walkthrough — 2026-06-18 (17 days)

Note: Appraisal and loan approval are the critical path right now. Both are due on the same
date and neither is confirmed complete. Sarah should contact the lender today.
```

**Document Checklist updated** — appraisal and loan approval rows marked Watch with today's date.

**Running Timeline entry:**
```
2026-06-01 — Transaction Coordinator — Due diligence check-in. 1 of 5 flags complete
(inspection). Appraisal and loan approval both due 2026-06-05 — Watch status.
Sarah advised to contact lender today to confirm both are on track.
```

---

## Example 3: Seller Initial Activation (Patricia Odom)

**Context:** An offer on 812 Ridgewood Trail was accepted. Contract signed 2026-07-10. Close date: 2026-08-08 (29-day close). Diana is the assigned agent.

**Received from orchestrator:**
```
client_name:         Patricia Odom
intent:              sell
contract_date:       2026-07-10
close_date:          2026-08-08
lead_object_path:    clients/open/patricia_odom/lead_object.json
contact_log_path:    clients/open/patricia_odom/contact_log.md
```

**Step 1 — Update `lead_object.json`:**
```json
{
  "contract_date": "2026-07-10",
  "close_date": "2026-08-08",
  "last_updated": "2026-07-10",
  "due_diligence": {
    "inspection": false,
    "appraisal": false,
    "loan_approval": true,
    "title_search": false,
    "contingencies_released": false
  }
}
```
Note: `loan_approval` is pre-set to `true` — not applicable to seller leads, not a blocker.

**Step 2 — Generate deadline map (counting back from 2026-08-08):**

| Item | Target Date | Notes |
|---|---|---|
| Seller disclosures delivered | 2026-07-13 | contract_date + 3 days |
| Buyer inspection access confirmed | 2026-07-13 | Ensure property is accessible |
| Inspection response deadline | 2026-07-20 | contract_date + 10 days — seller must decide repair/credit/as-is |
| Appraisal access | 2026-07-24 | contract_date + 14 days |
| Appraisal received and confirmed | 2026-07-25 | close_date − 14 days |
| Title preparation complete | 2026-07-29 | close_date − 10 days |
| Contingencies released | 2026-07-29 | close_date − 10 days |
| Final walkthrough access | 2026-08-07 | close_date − 1 day — property clear, agreed condition |

**Step 3 — Write Document Checklist (seller version):**

```
## Document Checklist

Generated: 2026-07-10  |  Contract date: 2026-07-10  |  Close date: 2026-08-08

### Due Diligence Deadlines

| Item | Target Date | Status | Notes |
|---|---|---|---|
| Seller disclosures delivered | 2026-07-13 | Pending | |
| Buyer inspection access confirmed | 2026-07-13 | Pending | |
| Inspection response (repair/credit/as-is) | 2026-07-20 | Pending | |
| Appraisal access provided | 2026-07-24 | Pending | |
| Appraisal received and confirmed | 2026-07-25 | Pending | |
| Title preparation complete | 2026-07-29 | Pending | |
| Contingencies released | 2026-07-29 | Pending | |
| Final walkthrough access | 2026-08-07 | Pending | |

### Documents Received

| Document | File | Date Received |
|---|---|---|
| (none yet) | — | — |

### Clear-to-Close Checklist
[Generated when all due diligence items are complete]
```

**Step 4 — 48-hour action list:**
```
Next 48 hours for Patricia Odom (seller — 812 Ridgewood Trail):
- Deliver all required seller disclosures to buyer's agent by 2026-07-13
- Confirm property is accessible for buyer's inspection — expect the inspection to be
  scheduled in the next 3–5 days
- Confirm title company has been notified and has the executed contract
- Note: This is an estate sale — confirm Patricia has legal authority to execute and
  that any required probate steps are complete before close
```

Note on estate_sale constraint: the TC surfaces the legal coordination flag as a reminder because `estate_sale` is in `constraints.tags`. This is not a blocker the TC manages — it is a reminder for Diana to verify.

**Running Timeline entry:**
```
2026-07-10 — Transaction Coordinator — Deal activated at under_contract. Contract date:
2026-07-10. Close date: 2026-08-08. Seller deadline map generated. loan_approval pre-set
true (seller lead). First deadlines: disclosures and inspection access by 2026-07-13.
Diana advised to confirm estate sale legal authority is clear before close.
```
