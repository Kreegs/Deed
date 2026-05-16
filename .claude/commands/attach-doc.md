# /attach-doc [client] [doc_type] [filename]

Attaches a document from the `inbox/` folder to a client's deal file. Sets the corresponding due diligence flag, updates the contact log, and routes to the transaction coordinator. Run this any time a document arrives mid-deal — inspection report, appraisal, title commitment, loan approval, or contingency release.

**Before running:** Drop the file into the `inbox/` folder at the root of the agency-system directory.

---

## Steps

1. Look for `[filename]` in `inbox/`. If the file is not found, stop and tell the agent: "File not found in inbox/. Drop the file there and try again." Do not proceed.

2. Read `active_cases.md` to find the client row matching `[client]`. If no match is found, say so. If more than one client shares the name, ask the agent to confirm before proceeding.

3. Map `[doc_type]` to the correct `due_diligence` flag using the table below. If the value does not match any accepted phrase, ask the agent to clarify before proceeding.

   | doc_type (accepted values) | Accepted aliases | Flag set in lead_object.json |
   |---|---|---|
   | `inspection_report` | "inspection", "inspection report", "inspection findings", "inspection results" | `inspection` |
   | `appraisal` | "appraisal", "appraisal report", "appraisal value" | `appraisal` |
   | `loan_approval` | "loan approval", "final approval", "clear to fund", "underwriting approval" | `loan_approval` |
   | `title_search` | "title search", "title commitment", "title report", "title clear" | `title_search` |
   | `contingencies_released` | "contingencies released", "contingency waiver", "all contingencies cleared" | `contingencies_released` |

4. Build the destination filename using the naming convention:

   ```
   [client_name_slug]_[doc_type]_[YYYYMMDD].[ext]
   ```

   - `client_name_slug` is the client folder name (underscores, lowercase)
   - `doc_type` is the canonical value from the table above (underscores, lowercase)
   - `YYYYMMDD` is today's date
   - `ext` is the original file extension

   Example: `marcus_webb_inspection_report_20260514.pdf`

5. Present the intended move to the agent for confirmation:

   > "Found Marcus Webb's folder. I'll move `home_inspection_final.pdf` from inbox/ as `marcus_webb_inspection_report_20260514.pdf` — confirm?"

   Do not touch any files until the agent confirms.

6. After confirmation, run a bash command to move the file from `inbox/[filename]` to `clients/open/[client_name]/[destination_filename]`. This deletes the file from `inbox/` — no copy is left behind.

7. Set the corresponding `due_diligence` flag to `true` in `clients/open/[client_name]/lead_object.json`.

8. Append a row to the Document Checklist section of `clients/open/[client_name]/contact_log.md`:

   ```
   | [destination_filename] | [today's date] | Received |
   ```

9. Read all `due_diligence` flags in `lead_object.json` and report what is still outstanding:

   > "Inspection received. Still outstanding: loan approval, title search, contingencies released."

   If all applicable flags are now `true`, notify the agent and trigger the automatic stage advance to `clear_to_close` per the orchestrator's gate rules.

10. Route to `04_transaction_coordinator` with the document path and full client context for review.

---

## Notes

- **Seller clients:** `loan_approval` is pre-set to `true` for seller leads and is not a blocker. Exclude it from the outstanding items report when `intent` is `"sell"`.
- **inbox/ is a staging area, not storage.** Files should not accumulate there. If the move fails for any reason, tell the agent so they can retry.
- **The agent's word is accepted.** No document parsing or content verification happens. If the agent says it is an inspection report, it is an inspection report. Accuracy is the agent's responsibility.
