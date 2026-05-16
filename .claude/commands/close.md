# /close [client]

Closes a deal and archives the client folder. Verifies all due diligence flags before executing — this is a hard gate. Run this when an agent confirms the deal has closed at the table.

---

## Steps

1. Read `active_cases.md` to find the client row. If no match, say so and stop.

2. Read `clients/open/[client_name]/lead_object.json`. Check the `due_diligence` object:
   - For buyer leads (`intent` is `"buy"`): all five flags must be `true` — `inspection`, `appraisal`, `loan_approval`, `title_search`, `contingencies_released`.
   - For seller leads (`intent` is `"sell"`): four flags must be `true` — `inspection`, `appraisal`, `title_search`, `contingencies_released`. Exclude `loan_approval` (pre-set to `true`, not a blocker).

   If any applicable flag is still `false`, **stop**. Do not proceed. Surface exactly which items are open:

   > "[Client name] cannot be closed — the following due diligence items are still open: [list]. Confirm these are complete before closing."

3. If all applicable flags are `true`, confirm with the agent before taking any action:

   > "All due diligence is complete for [Client Name]. This will:
   > - Set stage to 'closed'
   > - Send the congratulations closing message
   > - Move the client folder to clients/closed/
   > - Remove them from active_cases.md
   > - Update client_registry.md
   >
   > Confirm to proceed?"

   Do not execute any steps until the agent confirms.

4. After confirmation, execute in order:

   **a.** Set `stage` → `"closed"` and `last_updated` → today's date in `lead_object.json`.

   **b.** Append a closing entry to the Running Timeline in `contact_log.md`:
   ```
   [date] — Orchestrator — Deal closed. All due diligence complete. Client folder archived.
   ```

   **c.** Route to `03_client_communication` with communication type `congratulations_closing`. Pass client name, assigned agent (for voice profile), and close date from `lead_object.json`. Wait for the draft to be produced before proceeding.

   **d.** Run a bash command to move the entire client folder:
   ```
   mv clients/open/[client_name]/ clients/closed/[client_name]/
   ```
   On Windows use `Move-Item`. The folder is never deleted — only moved.

   **e.** Remove the client's row from `active_cases.md`.

   **f.** Update `clients/client_registry.md`: set `Closed Date` to today, update `Folder` column to `closed/[client_name]`.

5. Confirm completion to the agent:

   > "[Client Name] is closed. Folder archived to clients/closed/. Congratulations message is ready for review."

---

## Notes

- The congratulations message draft is surfaced to the agent for review before sending — the agent sends it. It is not auto-sent.
- If the bash move fails for any reason, stop and report the error. Do not update `active_cases.md` or `client_registry.md` until the folder has actually moved — these two updates confirm the archive completed.
- This command does not apply to leads that go cold without closing. Cold lead archival is handled separately by the orchestrator.
