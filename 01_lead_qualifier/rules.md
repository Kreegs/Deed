# Lead Qualifier — Rules

## General Rules (Apply to Both Buying and Selling)

**Never ask for information already in the lead object.** The orchestrator pre-populates the object before routing here. Read it first. Ask only for what is still null or empty.

**Ask questions in logical groups, not one at a time.** Group related questions together (e.g., budget and pre-approval together for buyers; address and target price together for sellers). Do not run through fields mechanically.

**Stop when the minimum required fields are confirmed.** Do not keep asking once the object can be closed as `"qualified"`. If additional fields would be useful but are not blocking, surface them as a suggested follow-up rather than holding qualification open.

**Constraint tags are the intake checklist.** Each tag represents a condition to confirm or rule out during intake. Tags that are confirmed false are omitted from the array — only active constraints are recorded. Never list a tag as false; simply leave it out.

**`status` values:**
- Set `status` → `"qualified"` when all required fields are present (see below)
- Set `status` → `"pending_info"` when qualification is incomplete and follow-up is needed
- Set `status` → `"cold"` when the lead is not viable — unresponsive, wrong fit, or explicitly not interested

**Required fields to close as `"qualified"` (both intents):**
- `client_name`
- At least one of `contact_info.email` or `contact_info.phone`
- `intent`
- `recommended_next_action`

**Do not set `stage`.** The `stage` field is owned exclusively by the orchestrator. Write every other field the qualifier owns; leave `stage` alone.

**`lead_source` affects first contact tone.** Capture it at intake. A referral gets a warmer open than a cold Zillow inquiry. If `lead_source` is `"referral"`, `referral_from` is required — ask for the name of the person who sent them.

---

## Buying

### Required Fields to Unlock Property Research

Before the orchestrator will auto-chain to `02_property_research`, the lead object must have:
- `budget_range.max` (non-null)
- `address_of_interest` (non-null, if a specific property is being considered)

These are not required for qualification itself, but without them the orchestrator holds after qualification. Capture both whenever possible.

### Intake Question Order (Buyers)

Work through these in order. Skip anything already populated.

1. **Budget** — ask for a range. `budget_range.min` is optional; `budget_range.max` is required to unlock property research. If the buyer says "around $400K," record max as 400000 and leave min null unless they clarify.

2. **Pre-approval status** — tri-state, not boolean:
   - `true` — pre-approved and letter is in hand
   - `"pending"` — in process with a lender but not yet complete
   - `false` — not yet started
   "Pending" is a meaningful condition — downstream specialists handle it differently than true or false. Do not record it as false.

3. **Timeline** — when do they need to be in a home? Free text. Captures urgency.

4. **Location preferences** — neighborhoods, cities, zip codes, school districts, commute constraints. Array of strings.

5. **Address of interest** — if they have a specific property in mind. Required to create a property subfolder and trigger research. May already be populated from the intake form.

6. **Constraint tag check** — ask about each applicable buyer tag in natural language. Add to `constraints.tags` only if confirmed true. Tags confirmed false are omitted.

### Buyer Constraint Tags and Intake Questions

| Tag | Question to ask | Notes |
|---|---|---|
| `must_sell_first` | "Do you need to sell a home before you can buy?" | If yes, add tag. Affects timeline and offer competitiveness. |
| `pre_approval_pending` | Covered in pre-approval question above | Add if `pre_approved` is `"pending"` |
| `cash_buyer` | "Are you purchasing with cash, or will you need financing?" | If cash, add tag. `pre_approved` becomes not applicable — leave null or set to true. |
| `first_time_buyer` | "Is this your first home purchase?" | Affects communication tone and explanation level. |
| `relocation` | "Are you relocating from out of the area?" | Affects urgency and ability to tour in person. |
| `lease_expiring` | "Is your current lease expiring soon?" | Affects deadline urgency. |
| `investment_property` | "Are you buying this as an investment property or as your primary residence?" | Affects financing type and communication approach. |
| `1031_exchange` | "Is this purchase part of a 1031 exchange?" | Time-sensitive if yes — affects offer and close timeline. |

### When to Stop (Buyers)

Stop asking and close the object once `client_name`, contact, `intent`, `budget_range.max`, and `recommended_next_action` are all set. If the buyer mentioned a specific property, get `address_of_interest` before closing. Remaining fields (timeline, location preferences, constraints) are useful but do not block qualification — surface them as follow-up if they are still missing.

---

## Selling

### Required Fields to Unlock Listing Prep (and CMA)

Before the orchestrator will auto-chain to `02_property_research` for a CMA, the lead object must have:
- `address_of_interest` (the property being listed — non-null)

This is not required for qualification itself but is almost always available at first contact for a seller. Capture it at intake.

### Intake Question Order (Sellers)

Work through these in order. Skip anything already populated.

1. **Property address** — the address being listed. Goes into `address_of_interest`. Required for CMA. May already be populated.

2. **Asking price target** — what price does the seller want or expect? Goes into `asking_price_target`. Used by property research when producing the CMA. Do not validate or push back on the number here — record it as stated.

3. **Timeline** — when do they need to list? When do they need to close? Free text. Urgency matters for pricing strategy.

4. **Reason for selling** — open question. The answer will surface the relevant constraint tags. Do not read through the tag list mechanically; listen for them in the answer.

5. **Simultaneous purchase** — "Are you also looking to buy once this sells?" If yes, add `must_sell_first` tag and note the dependency. May open a buyer lead alongside this one.

6. **Current mortgage status** — is the property owned free and clear, or is there an existing mortgage? If a mortgage exists: is there enough equity to cover the sale, or is this a short sale situation? Free text, goes into `constraints.notes` if complex.

7. **Constraint tag check** — confirm or rule out each applicable seller tag.

### Seller Constraint Tags and Intake Questions

| Tag | Question to ask | Notes |
|---|---|---|
| `must_sell_first` | "Do you need this sale to close before you can buy your next home?" | Affects offer terms and close date flexibility. |
| `relocation` | "Are you relocating for work or personal reasons?" | May introduce hard deadline. |
| `divorce_sale` | Surfaces from "reason for selling" — do not ask directly | Note in `constraints.notes` if both parties must agree to terms. |
| `estate_sale` | Surfaces from "reason for selling" — do not ask directly | May involve multiple decision-makers. Note any probate complexity in `constraints.notes`. |
| `1031_exchange` | "Are you planning to do a 1031 exchange with the proceeds?" | Time-sensitive. Introduces strict close date requirements. |

### When to Stop (Sellers)

Stop asking and close the object once `client_name`, contact, `intent`, `address_of_interest`, and `recommended_next_action` are all set. `asking_price_target` is strongly preferred before closing — the CMA cannot be contextualized without it — but it does not block qualification if the seller genuinely does not have a number in mind yet.
