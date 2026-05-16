# Client Communication — Rules

## Voice Profiles

Every piece of outbound communication must be drafted in the voice of the assigned agent.

Before drafting any message:

1. Read `assigned_agent` from the context passed by the orchestrator.
2. Open `03_client_communication/voice_profiles/[assigned_agent].md`.
3. Read all sections — tone, openings, how they handle bad news, sign-off, example lines.
4. Draft the communication as if the assigned agent wrote it themselves.

If the voice profile file does not exist for the assigned agent, stop and alert the agent: "No voice profile found for [name]. Please complete `voice_profiles/[name].md` before drafting communications."

Never draft in a generic voice. Never average across multiple profiles. One agent, one voice, per communication.

---

## General Drafting Rules

**Read the full context before drafting.** The orchestrator passes deal stage, contact preference, a contact_log excerpt, and any deadline or constraint the draft should reference. Use all of it. A generic email is a failure.

**Pull from the contact_log.** The running timeline and current state tell you what has happened, what the client was last told, and what they are waiting on. The draft should feel like a continuation of the relationship, not a fresh start.

**Respect contact preference.** `contact_info.preferred_method` in the lead object is "email", "phone", or "text." This specialist drafts email copy by default. If preferred method is "text" or "phone," note that at the top of the draft — the agent may want to adjust format or call instead.

**Reference deadlines when they exist.** If the contact_log or lead object contains a deadline relevant to the communication (lease expiring, close date, offer expiration, due diligence item due), include it. Clients need to understand urgency when it is real.

**Drafts go in `email_drafts/`.** Write every draft to `clients/open/[client_name]/email_drafts/[communication_type]_[YYYYMMDD].md`. Never overwrite an existing draft — use the date to differentiate.

**Update `contact_log.md` when the draft is complete.** Append a Running Timeline entry and overwrite the Current State section. The log entry records that a draft was written, not that the email was sent — the agent sends it.

---

## Buying

Templates that apply to buyer clients, in deal order:

| Stage | Communication type | Template |
|---|---|---|
| `property_search` | First outreach to a new buyer | `first_contact` |
| `property_search` | Sending a property or shortlist | `property_info` |
| `offer_pending` | Notifying buyer their offer is submitted | `offer_submitted` |
| `offer_pending` | Relaying a counter-offer | `counter_offer_response` |
| `under_contract` | What happens now that they're under contract | `under_contract_intro` |
| `due_diligence` | Communicating inspection findings and response | `inspection_response` |
| `clear_to_close` / `closing` | What to expect in the final days | `closing_prep` |
| `closed` | Post-close congratulations | `congratulations_closing` |
| Any stage | General check-in or progress update | `general_update` |
| Any stage | Re-engaging a quiet lead | `cold_lead_follow_up` |
| Any stage | Notifying client of a delay | `closing_delay` |

**Buyer-specific tone guidance:**

- `first_contact`: Lead source affects warmth. A referral gets a warmer open — reference the person who sent them. A Zillow lead gets a confident, helpful open without being pushy.
- `property_info`: Be specific about the property. Pull from the property report. If the property has a condition flag or is over budget, note it honestly — do not oversell.
- `counter_offer_response`: Present the counter factually. Do not push the buyer toward accept or reject — lay out the options and let them decide.

---

## Selling

Templates that apply to seller clients, in deal order:

| Stage | Communication type | Template |
|---|---|---|
| `listing_prep` | Walking the seller through the listing agreement | `listing_agreement_intro` |
| `active_listing` | Summary of showing activity and feedback | `showing_feedback_summary` |
| `offer_received` | Presenting an incoming offer to the seller | `offer_received_summary` |
| `active_listing` | Broaching a price adjustment | `price_reduction_discussion` |
| `under_contract` | What happens now that they're under contract | `under_contract_intro` |
| `due_diligence` | Communicating the buyer's inspection requests | `inspection_response` |
| `clear_to_close` / `closing` | Final steps before close | `closing_prep` |
| `closed` | Post-close congratulations | `congratulations_closing` |
| Any stage | General check-in or progress update | `general_update` |
| Any stage | Re-engaging a quiet lead | `cold_lead_follow_up` |
| Any stage | Notifying client of a delay | `closing_delay` |

**Seller-specific tone guidance:**

- `listing_agreement_intro`: Do not overwhelm with legal detail. Summarize what they are agreeing to, what happens next, and what to expect from the agent during the listing period.
- `showing_feedback_summary`: Be honest about feedback, especially if it is negative. Sellers need accurate feedback to make decisions. Soft-pedaling bad feedback leads to overpriced listings that sit.
- `price_reduction_discussion`: Lead with data, not opinion. The case for a reduction should come from the market — days on market, showing patterns, comp trends — not from the agent's gut. Make the recommendation clearly and let the seller respond.
- `offer_received_summary`: Present the offer terms factually — price, contingencies, close date, any unusual terms. Then outline the seller's options: accept, reject, counter. Do not recommend a course of action in the email unless the agent explicitly wants that framing.
