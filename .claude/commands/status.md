# /status — Full Team Briefing

Produces a three-bucket status report across all open cases. Run this any time to see where every deal stands, what is stale, and what needs attention today.

This is a read-only command. It does not modify any files.

---

## Steps

1. Read `active_cases.md` to get the full list of open clients. Each row gives: client name, folder path, stage, assigned agent, and last contact date.

2. For each client, read two files:
   - `clients/open/[client_name]/contact_log.md` — extract:
     - **Running Timeline**: date of the most recent entry (the first entry at the top)
     - **Current State**: `Next action` and `Deadline` fields
     - **Document Checklist**: only relevant for `due_diligence` stage — read the Due Diligence Deadlines table for items with Target Dates and their Status
   - `clients/open/[client_name]/lead_object.json` — read `stage` and `intent` as a cross-check

   If a client folder or contact_log is missing, flag it as **Urgent**: "Client folder or contact_log not found — needs investigation."

3. Determine each client's status using the rules below. Apply stale threshold or deadline check depending on stage.

---

## Stale Thresholds (non-due_diligence stages)

Compare the date of the most recent Running Timeline entry against today's date.

| Stage | Flag as Watch after | Flag as Urgent after |
|---|---|---|
| `qualifying` | — | 1 day |
| `qualified` | — | 2 days |
| `property_search` / `listing_prep` | 3 days | 5 days |
| `offer_pending` / `offer_received` | — | 1 day |
| `active_listing` | 3 days | 5 days |
| `clear_to_close` / `closing` | — | 1 day |

"Flag as Watch after" means: days since last contact is at or above this number but below the Urgent threshold.

If a stage is not in this table, apply a 5-day Urgent threshold and surface it as unusual.

---

## Due Diligence Cases (deadline-based check)

For clients in `due_diligence` stage, ignore elapsed time since last contact. Instead, read the **Due Diligence Deadlines** table in the Document Checklist section of their `contact_log.md`.

For each row where Status is not `Complete`:
- Target Date is **past** → **Urgent**
- Target Date is within **48 hours** → **Urgent**
- Target Date is within **3 days** → **Watch**
- Target Date is more than 3 days away → **On Track**

If any item is Urgent, the whole client is Urgent. If any item is Watch (and none are Urgent), the client is Watch. If all items are On Track, the client is On Track.

For seller clients (`intent` is `"sell"`), exclude `loan_approval` from this check — it is pre-set to `true` and is not a blocker.

---

## Output Format

Sort output: **Urgent** first, then **Watch**, then **On Track**.

```
Team Briefing — [today's date]
[N] open cases  |  [N] urgent  |  [N] watch  |  [N] on track

---

## Urgent

**[Client Name]** — [Stage] — Assigned: [Agent]
Last contact: [date] ([N] days ago)
Next action: [value from Current State]
Why flagged: [specific reason — "9 days since last contact, threshold is 5" or "Inspection deadline past due"]
Suggested: [one concrete action the agent should take today]

[repeat for each Urgent client]

---

## Watch

**[Client Name]** — [Stage] — Assigned: [Agent]
Last contact: [date] ([N] days ago)
Next action: [value from Current State]
Why flagged: [specific reason]
Suggested: [one concrete action]

[repeat for each Watch client]

---

## On Track

- [Client Name] — [Stage] — [Agent] — Last contact [date]
[one line per client]
```

If a bucket is empty, omit the section header entirely.

If there are no open cases, output: "No open cases in active_cases.md."

---

## Entry tone

Entries in Urgent and Watch should be specific, not vague. Name the gap:

- Good: "Susan Chen / Property Search / last contact 8 days ago / next action was to send a shortlist of properties / suggested: send the shortlist today."
- Bad: "Susan is quiet."

The suggested action should be something the assigned agent can do in the next session — not a general observation.

---

## Notes

- Last contact date comes from the most recent Running Timeline entry in `contact_log.md`, not from `active_cases.md`. The `active_cases.md` column is informational; the log is authoritative.
- If the contact_log has no Running Timeline entries yet (new lead, no specialist has run), use the `created_date` from `lead_object.json` as the baseline.
- Do not read or surface email draft content, property reports, or any data beyond what is needed for the status check.
