# Template: Closing Prep

## When to Use
Sent to buyer or seller when the deal reaches `clear_to_close` or `closing` stage. Prepares the client for the final steps before closing day. Intent determines the version.

## Context to Read
- `intent` — determines buyer or seller version
- `close_date` — the closing date; everything in this email is anchored to it
- `contact_log` Document Checklist — what is confirmed complete, what remains
- `due_diligence` flags — all should be true by now; if any remain false, do not send this email; alert the orchestrator instead
- `constraints.tags` — anything affecting final steps (e.g., `1031_exchange` means proceeds timing is critical)

## Subject Line
`Closing Is [X Days Away] — Here's What to Expect`

## Required Content (in order)

### Buyer Version

1. **Close date** — state it at the top. The client should be counting down.

2. **Final walkthrough** — when it is scheduled (or ask the client to confirm availability). Typically 24 hours before closing.

3. **Wire transfer / funds** — the client needs to know exactly how much to wire and where to send it. Note that wire instructions will come from the title company and they should call to verify before sending — wire fraud is real. Do not include actual wire amounts or routing numbers in the email.

4. **What to bring to closing** — government-issued ID, any remaining signed documents, checkbook as backup.

5. **What to expect at the closing table** — brief. How long it takes, what they are signing, when they get keys.

6. **Anything still outstanding** — if the contact_log Document Checklist has items pending, note them. If everything is clear, say so.

7. **Close** — brief congratulation on getting here. Keep it warm.

### Seller Version

1. **Close date** — state it at the top.

2. **Final walkthrough** — buyer will walk the property; remind seller to leave the property in agreed condition and that any agreed items (appliances, fixtures) must be in place.

3. **Wire / proceeds** — the seller should know approximately what they will net (agent has likely discussed this). Confirm that the title company will reach out with final settlement statement before closing.

4. **What to bring** — government-issued ID, keys and garage openers for transfer, any manuals or warranty documents they are leaving.

5. **What to expect at closing** — brief. Deed transfer, final signatures, how and when proceeds are released.

6. **Moving coordination** — if the seller still needs to vacate, note the agreed possession date.

7. **Close** — warm and brief. This is a milestone.

## Tone Notes
- This email should feel organized and reassuring. Closing is stressful and clients appreciate having a clear checklist.
- Be specific. Vague closing prep emails do not help clients know what to do.
- Do not include wire amounts or account numbers — reference the title company for all financial specifics.

## Do Not Include
- Wire routing numbers or account details
- Legal explanations of what they are signing — keep it practical
- Anything that should have been handled earlier in due diligence — if something is still unresolved, escalate rather than mentioning it here
