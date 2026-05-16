# Client Communication — Examples

Two worked examples: one buyer first contact, one seller listing agreement intro. Each shows the full input-to-draft flow including voice profile application.

---

## Example 1: Buyer First Contact (Kevin Marsh / Sarah)

**Context:** Kevin Marsh has just been qualified and property research on 204 Creekside Dr is complete. The orchestrator asked if the agent wants to draft a first contact email. Agent confirmed yes.

**Received from orchestrator:**
```
client_name:         Kevin Marsh
intent:              buy
communication_type:  first_contact
assigned_agent:      Sarah
deal_stage:          property_search
contact_preference:  text
context:             New buyer lead from Zillow. Pre-approved, budget $350K–$390K, targeting
                     Cedarville/Millbrook. Lease expires August 2026. Interested in 204 Creekside Dr,
                     Millbrook. Property report complete — priced at $374,900, within
                     budget, move-in ready, comp-supported. No red flags. Referred by nobody —
                     came in cold through Zillow search.
lead_object_path:    clients/open/kevin_marsh/lead_object.json
contact_log_path:    clients/open/kevin_marsh/contact_log.md
```

**Step 1 — Read voice profile:** Open `voice_profiles/sarah.md`. Sarah is direct and energetic, uses first names immediately, leads with useful information rather than pleasantries, and has a casual but confident sign-off.

**Step 2 — Read template:** Open `templates/first_contact.md`. Lead source is Zillow (not a referral), so no referral name-drop. Include the specific property, confirm you have the details they submitted, offer a next step.

**Step 3 — Note contact preference:** `preferred_method` is "text." Add note at top of draft.

---

**Draft written to `email_drafts/first_contact_20260512.md`:**

```
To:      kevin.marsh@gmail.com
Subject: 204 Creekside Dr — Let's Talk

[NOTE: Kevin prefers text. Consider whether this is better sent as a text or short message
rather than email. Full email copy below.]

---

Hi Kevin,

Saw your inquiry on Zillow for 204 Creekside Dr in Millbrook — great timing on that one.
I pulled the details and ran some comps. It's priced right at market and it's vacant,
so you could move fast if it's the right fit.

Given your August timeline, I'd love to get you in for a showing this week while it's
still available. I can also put together a quick shortlist of a few other properties in
Millbrook and Cedarville in your range so you have something to compare it against.

Are you free for a showing Thursday or Friday? Or let me know what works and I'll make it happen.

Talk soon,
Sarah
```

---

**contact_log update (Running Timeline):**
```
2026-05-12 — Client Communication — first_contact draft written for Kevin Marsh. Focused on
204 Creekside Dr with offer to schedule showing and build a comparison shortlist. Noted client
prefers text — agent should consider delivery method before sending.
```

**contact_log update (Current State):**
```
Current stage:     property_search
Last action:       first_contact draft written — awaiting agent review and send
Next action:       Sarah to review draft at email_drafts/first_contact_20260512.md,
                   confirm send method (text vs email), and send
Open questions:    Client prefers text — agent may want to condense or send via text instead
Deadline:          Kevin's lease expires August 2026
```

---

## Example 2: Seller Listing Agreement Intro (Patricia Odom / Diana)

**Context:** Patricia Odom's CMA is complete. Diana reviewed it and had the pricing conversation with Patricia. Patricia agreed to list at $515,000 and is ready to sign the listing agreement.

**Received from orchestrator:**
```
client_name:         Patricia Odom
intent:              sell
communication_type:  listing_agreement_intro
assigned_agent:      Diana
deal_stage:          listing_prep
contact_preference:  email
context:             CMA complete. Agreed list price: $515,000. Patricia is executor of her
                     late mother's estate. Constraint tags: relocation, estate_sale. Moving to
                     Denver. 90-day timeline to list and close. Diana has walked the property.
                     Patricia is ready to sign the listing agreement.
lead_object_path:    clients/open/patricia_odom/lead_object.json
contact_log_path:    clients/open/patricia_odom/contact_log.md
```

**Step 1 — Read voice profile:** Open `voice_profiles/diana.md`. Diana is warm but efficient, leads with what the client needs to know, handles complex situations with calm authority, and signs off with her full name.

**Step 2 — Read template:** Open `templates/listing_agreement_intro.md`. Seller is ready to sign. Cover what the agreement says, what they're committing to, and what happens next. Note estate_sale context — acknowledge the emotional weight briefly and move to practical.

---

**Draft written to `email_drafts/listing_agreement_intro_20260512.md`:**

```
To:      patricia.odom@email.com
Subject: Listing Agreement — 812 Ridgewood Trail

Hi Patricia,

It was great walking through the house with you. I know this process carries a lot with it,
and I want to make sure you feel clear and comfortable at every step.

Here's what the listing agreement covers and what to expect from here:

**What you're signing:** A 90-day exclusive listing agreement authorizing our team to
market 812 Ridgewood Trail at $515,000. This gives us the right to list the property on
MLS, schedule showings, and negotiate on your behalf.

**What we'll do next:** Once the agreement is signed, we'll schedule professional photos,
write the listing description, and get the property on MLS within the week. I'll send you
the listing for review before it goes live.

**What to expect during the listing:** I'll send you a showing summary every week — how many
showings, what buyers said, how the activity compares to other homes in the area. If anything
comes in that needs a decision from you, I'll be in touch the same day.

**A note on timing:** With your 90-day window, we'll want to see strong early activity. If
showing volume is low in the first few weeks, we'll have an honest conversation about
adjusting price or strategy before too much time passes.

I'll send the listing agreement over through DocuSign shortly. Let me know if you have any
questions before you sign.

Warm regards,
Diana
```

---

**contact_log update (Running Timeline):**
```
2026-05-12 — Client Communication — listing_agreement_intro draft written for Patricia Odom.
Covers agreement terms, next steps (photos, MLS), weekly showing summaries, and 90-day
timing context. DocuSign to follow.
```

**contact_log update (Current State):**
```
Current stage:     listing_prep
Last action:       listing_agreement_intro draft written — awaiting agent review and send
Next action:       Diana to review draft and send; follow with DocuSign for listing agreement
Open questions:    None — Patricia is ready to sign
Deadline:          90-day listing window begins when agreement is executed
```
