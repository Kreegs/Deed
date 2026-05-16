# /dd-status [client]

Produces an immediate due diligence status snapshot for a client currently in the `due_diligence` stage. Run this any time during an active transaction to see what is complete, what is at risk, and what needs to happen next — without invoking the full Transaction Coordinator workflow.

This is a read-only command. It does not modify any files.

---

## Steps

1. Read `active_cases.md` to find the client. If no match, stop.

2. Read `clients/open/[client_name]/lead_object.json`:
   - Confirm `stage` is `"due_diligence"`. If not, inform the agent: "[Client] is in `[stage]` — `/dd-status` only applies to clients in due_diligence."
   - Read `intent`, `close_date`, `contract_date`, and the full `due_diligence` flags object.

3. Read the **Document Checklist** section of `clients/open/[client_name]/contact_log.md`:
   - Extract the Due Diligence Deadlines table — each item, its Target Date, and its Status.
   - Extract the Documents Received table.

4. For each item in the deadline table where Status is not `Complete`:
   - Compare Target Date to today's date.
   - **Past due** → Urgent
   - **Within 48 hours** → Urgent
   - **Within 3 days** → Watch
   - **More than 3 days away** → On Track

5. Cross-reference the deadline table items against the `due_diligence` flags in `lead_object.json`:
   - If a flag is `true` but the checklist item is still marked Pending, note the discrepancy — the checklist may need updating.

6. Output the status report (format below).

---

## Output Format

```
Due Diligence Status — [Client Name] — [today's date]
Stage: due_diligence  |  Contract date: [date]  |  Close date: [date]  |  [X] days to close

✓ Complete
  - [item] — received [date]
  [or "None complete yet" if all flags are false]

🔴 Urgent (past due or due within 48 hours)
  - [item] — due [date] ([X days past due / "due tomorrow" / "due today"])
  - Action: [specific recommended next step]
  [omit section if none]

⚠ Watch (due within 3 days)
  - [item] — due [date] ([X days remaining])
  [omit section if none]

⬜ Pending
  - [item] — due [date] ([X days remaining])
  [omit section if none]

Documents received: [N]
  - [filename] — [date received]
  [or "None yet" if the table is empty]
```

If all applicable flags are `true`:
> "All due diligence items are complete for [Client Name]. Ready to advance to clear_to_close — confirm with the orchestrator."

---

## Notes

- For seller clients (`intent` is `"sell"`): exclude `loan_approval` from all checks. It is pre-set to `true` and is not a blocker.
- "Days to close" is calculated from today to `close_date`. If `close_date` is null, flag it: "Close date not confirmed — deadline map cannot be evaluated accurately."
- This command reads and displays only. To update flags or the Document Checklist, use `/attach-doc` for document intake or tell the orchestrator directly (e.g., "loan approval is confirmed for Marcus").
- For a full TC review including document analysis and recommendations, route to `04_transaction_coordinator` through the orchestrator.
