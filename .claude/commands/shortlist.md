# /shortlist [client name]

Pulls all properties in a buyer's active search and displays them as a quick-reference comparison table. Run this when an agent wants to review the full property picture for a buyer — before a shortlist email, before a showing decision, or when a buyer asks "what are we working with?"

---

## Steps

1. Read `active_cases.md` to find the client row matching the name provided. If no match is found, say so. If more than one client shares the name, ask the agent to confirm.

2. Confirm `intent` is `"buy"`. This command does not apply to seller clients.

3. Read the `properties/` directory under `clients/open/[client_name]/`. For each subfolder, read `property_object.json` and, if available, the Summary section of `property_report.md`.

4. Output the comparison table below, sorted by status: `active` first, then `offer_pending`, then `offer_rejected`, then `removed`.

5. After the table, output a one-paragraph plain-language summary: how many active properties are in the search, any that stand out from the research, and a suggested next step if one is obvious (e.g., a property with no showing scheduled yet, or one where research is still pending).

---

## Output Format

```
Shortlist — [Client Name]
Stage: [deal stage]  |  Assigned agent: [agent]  |  Close target: [timeline or "not set"]

| # | Address | List Price | Beds/Ba/SqFt | Status | Research | Showing(s) | Notes |
|---|---|---|---|---|---|---|---|
| 1 | [address] | $[price] | [X/X/X,XXX] | Active | ✓ | [dates or "none"] | [buyer_notes truncated to 60 chars] |
| 2 | [address] | $[price] | [X/X/X,XXX] | Active | Pending | none | — |
| 3 | [address] | $[price] | [X/X/X,XXX] | Removed | ✓ | [date] | [notes] |
```

**Column notes:**
- **Research** — ✓ if `research_complete: true`; "Pending" if false
- **Showing(s)** — list all dates from `showing_dates`; "none" if empty
- **Notes** — `buyer_notes` from property_object.json, truncated; "—" if empty
- **Status** — use the raw status value from property_object.json (`active`, `offer_pending`, `offer_rejected`, `removed`)

If `property_details` are not yet populated (research pending), leave those cells as "—".

---

## After the Table

Plain-language summary paragraph. Example:

> Kevin has 3 active properties and 1 removed. Research is complete on two of them — 204 Creekside Dr and 456 Oak Ave. 789 Elm Blvd has research pending. Kevin has toured Creekside Dr but not the other two. Suggested next step: schedule showings on Oak Ave and Elm Blvd before narrowing down.

If the buyer has no properties yet, output: "[Client name] has no properties in their search yet. Use 'research [address] for [client]' to add one."

---

## This command is read-only

`/shortlist` does not modify any files. It reads and displays only.
