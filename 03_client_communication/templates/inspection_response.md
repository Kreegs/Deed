# Template: Inspection Response

## When to Use
After the inspection report is received and a response decision has been reached. Used during `due_diligence` stage. Buyer version communicates the inspection findings and proposed response to the seller. Seller version communicates the buyer's inspection requests and recommends how to respond.

## Context to Read
- `intent` — determines buyer or seller version
- Inspection findings — from context passed by orchestrator (summary of findings, what was flagged as significant)
- Proposed response — what the agent is recommending (repair credits, price reduction, specific repairs, or proceeding as-is)
- `close_date` — how much time is left; urgency of the response
- `contact_log` — any prior discussion about inspection expectations

## Subject Line
- Buyer version: `Inspection Results — [Address]`
- Seller version: `Buyer's Inspection Response — [Address]`

## Required Content (in order)

### Buyer Version

1. **Summary of findings** — plain-language summary of what the inspection found. Distinguish between significant issues (structural, systems, major repairs) and typical findings (minor items, normal wear). Do not list every item — summarize the material ones.

2. **The proposed response** — clearly state what the agent is recommending: repair request for specific items, a price reduction (amount), repair credit at closing, or proceeding as-is. Explain the reasoning briefly.

3. **What the buyer needs to do** — typically: confirm agreement with the proposed response so the agent can send it. If the buyer wants to discuss, invite a call.

4. **Timeline** — how long the buyer has to submit their response under the contract. State it clearly.

5. **Close** — direct. Ask the buyer to confirm or call.

### Seller Version

1. **The buyer's request** — state exactly what the buyer has asked for: specific repairs, credit amount, price reduction. Be factual and complete. Do not editorialize the buyer's position.

2. **The agent's assessment** — brief. Is the request reasonable given what the inspection found? Are there items worth pushing back on? One to three sentences of honest guidance.

3. **Seller's options** — lay out the paths: grant the request, offer a counter (partial credit, alternative repairs), or reject and risk the buyer walking. Be clear about the risk of each.

4. **Agent's recommendation** (if the agent wants it in the email) — one sentence. Advisory, not directive.

5. **Timeline** — seller's response deadline under the contract.

6. **Close** — ask the seller to call or confirm direction.

## Tone Notes
- Calm and solution-focused regardless of what the inspection found. Inspections almost always have findings. The goal is to resolve them, not to alarm the client.
- For significant findings, do not minimize — acknowledge what is real. But pair every problem with a path forward.
- This is a negotiation moment. The email should be clear and informative, not emotional.

## Do Not Include
- Every item on the inspection report — material findings only
- Alarmist language about normal inspection findings
- Predictions about whether the other side will accept the proposed response
