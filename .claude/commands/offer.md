# /offer [client] [address] [action] [price?]

Manages offer state for a buyer or seller client. Handles four actions: `submit`, `accept`, `reject`, `counter`. Updates property status, lead stage, and contact log, then routes to communication if a message is needed.

**Actions:**
- `submit` — buyer is submitting an offer on a property
- `accept` — an offer has been accepted (buyer's offer accepted, or seller accepting a received offer)
- `reject` — an offer was rejected (buyer's offer rejected, or seller rejecting a received offer)
- `counter` — a counter-offer has been made

**Price is required for `submit` and `counter`. Optional for `accept` and `reject`.**

Examples:
```
/offer marcus_webb 204_creekside_dr submit 485000
/offer marcus_webb 204_creekside_dr accept
/offer marcus_webb 204_creekside_dr reject
/offer marcus_webb 204_creekside_dr counter 492000
```

---

## Step 1 — Resolve client and property

1. Read `active_cases.md` to find the client. If no match, stop.
2. Read `clients/open/[client_name]/lead_object.json` for `intent` and current `stage`.
3. Locate the property subfolder under `clients/open/[client_name]/properties/[address_slug]/`. Read `property_object.json`.
   - If the address does not match any subfolder, ask the agent to confirm the address before proceeding.

---

## Action: submit

**Use when:** a buyer client is making an offer on a property.

**Steps:**

1. Confirm the property's current status is `active`. If it is `offer_rejected` or `removed`, confirm with the agent before proceeding — they may want to reactivate it first.

2. Confirm with the agent:
   > "Submitting offer of $[price] on [address] for [client]. This will set the property to offer_pending and advance the deal stage. Confirm?"

3. After confirmation:
   - Set `status` → `"offer_pending"` and `offer_price` → [price] in `property_object.json`.
   - Set lead `stage` → `"offer_pending"` in `lead_object.json`.
   - Append to Running Timeline in `contact_log.md`:
     ```
     [date] — Orchestrator — Offer submitted: $[price] on [address]. Stage advanced to offer_pending.
     ```
   - Overwrite Current State:
     ```
     Current stage:    offer_pending
     Last action:      Offer submitted — $[price] on [address]
     Next action:      Await seller response
     Open questions:   None
     Deadline:         None identified
     ```

4. Route to `03_client_communication` with type `offer_submitted`. Pass client name, assigned agent, property address, and offer price.

---

## Action: accept

**Use when:** a buyer's offer has been accepted by the seller, or a seller client is accepting a received offer.

**Steps:**

1. Confirm with the agent:
   > "Marking offer accepted on [address] for [client]. This will advance the deal to under_contract and invoke the Transaction Coordinator. Confirm?"

2. After confirmation:
   - Set `status` → `"accepted"` in the accepted property's `property_object.json`.
   - Set all other properties with status `active` or `offer_pending` → `"removed"` in their `property_object.json`. A buyer can only be under contract on one property.
   - Set lead `stage` → `"under_contract"` in `lead_object.json`.
   - Append to Running Timeline:
     ```
     [date] — Orchestrator — Offer accepted on [address]. Stage advanced to under_contract. TC invoked.
     ```
   - Overwrite Current State:
     ```
     Current stage:    under_contract
     Last action:      Offer accepted — [address]
     Next action:      Transaction Coordinator activated — deadline map and 48-hour action list incoming
     Open questions:   Confirm contract_date and close_date with agent
     Deadline:         Close date TBD — confirm with agent
     ```

3. Invoke `04_transaction_coordinator` for initial activation. Pass: client name, lead_object path, contact_log path, contract date (ask agent if not provided), and close date (ask agent if not provided).

---

## Action: reject

**Use when:** a buyer's offer was rejected by the seller, or a seller client is rejecting a received offer.

**Buyer rejection steps:**

1. Set `status` → `"offer_rejected"` in the property's `property_object.json`. Do not delete the folder.
2. Set lead `stage` → `"property_search"` in `lead_object.json`.
3. Append to Running Timeline:
   ```
   [date] — Orchestrator — Offer rejected on [address]. Stage rolled back to property_search.
   ```
4. Overwrite Current State:
   ```
   Current stage:    property_search
   Last action:      Offer rejected — [address]
   Next action:      Resume property search
   Open questions:   None
   Deadline:         None identified
   ```
5. Inform the agent: "Offer on [address] rejected. Stage rolled back to property_search. No specialist invoked — resume the search on the next interaction."

**Seller rejection steps:**

1. Append to Running Timeline:
   ```
   [date] — Orchestrator — Offer rejected. Stage remains active_listing.
   ```
2. Stage stays `"active_listing"` — no rollback needed.
3. Inform the agent: "Offer rejected. [Client] remains in active_listing."

---

## Action: counter

**Use when:** a counter-offer has been made (either direction).

**Steps:**

1. Append to Running Timeline:
   ```
   [date] — Orchestrator — Counter-offer at $[price] on [address].
   ```
2. Update `offer_price` → [counter price] in `property_object.json`.
3. Overwrite Current State:
   ```
   Current stage:    [current stage — unchanged]
   Last action:      Counter-offer submitted — $[price] on [address]
   Next action:      Await response to counter
   Open questions:   None
   Deadline:         None identified
   ```
4. Route to `03_client_communication` with type `counter_offer_response`. Pass client name, assigned agent, address, and counter price.

---

## Notes

- Stage changes in `lead_object.json` are always accompanied by setting `last_updated` to today's date.
- For `accept`: ask the agent for `contract_date` and `close_date` before invoking the TC if they are not already in `lead_object.json`. The TC cannot generate its deadline map without `close_date`.
- This command does not handle property removal without an offer — that is handled naturally by the orchestrator when the agent uses trigger phrases like "take that one off the list."
