# Template: Under Contract Intro

## When to Use
Sent to either a buyer or seller immediately after a contract is signed and the deal moves to `under_contract`. This is the "what happens now" email. Intent determines the version.

## Context to Read
- `intent` — buy or sell; determines which version to draft
- `close_date` from lead_object.json — the agreed closing date
- `contract_date` from lead_object.json — the date the contract was signed
- `assigned_agent` — voice profile
- `contact_log` Current State — any immediate next steps the TC has identified
- `constraints.tags` — anything that affects the due diligence process (e.g., `1031_exchange` means a hard close deadline; `estate_sale` may mean legal coordination is involved)

## Subject Line
`You're Under Contract — Here's What Happens Next`

## Required Content (in order)

### Buyer Version

1. **Congratulations** — brief, genuine. One sentence.

2. **The timeline** — state the close date and give a plain-language summary of what the due diligence period involves: inspection, appraisal, loan approval, title search, contingency releases. Do not go into legal detail — give the client a mental map of the next few weeks.

3. **What they need to do right now** — the immediate asks from the client. Typically: schedule the inspection, stay in contact with their lender, and do not make any large purchases or open new credit lines before closing.

4. **What the agent is handling** — one sentence on what the agent and TC are managing so the client does not have to worry about it.

5. **Close date** — state it clearly. It is the most important date in the deal from this point forward.

6. **Close** — reassure briefly. "You're in good hands" is the sentiment. Keep it short.

### Seller Version

1. **Congratulations** — brief and genuine. One sentence.

2. **The timeline** — state the close date and give a plain-language summary of what comes next: buyer's inspection and response, appraisal, title preparation, deed transfer. Focus on what the seller needs to understand, not the full technical process.

3. **What they need to do right now** — typically: keep the property accessible for the inspection, gather any documentation the agent may need (HOA records, warranties, etc.), and be responsive if the buyer's agent sends inspection requests.

4. **What the agent is handling** — one sentence.

5. **Close date** — state it clearly.

6. **Constraint notes** (if applicable) — if `estate_sale` or `divorce_sale` tags are present, briefly acknowledge that the agent will be in close contact to make sure coordination with legal/other parties goes smoothly.

## Tone Notes
- This email should feel like a handoff from celebration to execution. The excitement of going under contract is real, but the client needs to shift into "let's get this closed" mode.
- Do not overwhelm with detail. The client does not need to understand every due diligence item — they need to know what they have to do and that the agent has the rest covered.
- Keep it warm but efficient. Closing does not happen by itself.

## Do Not Include
- Detailed legal explanation of contingencies
- Long list of everything that could go wrong
- Excessive celebration that undersells how much work remains
