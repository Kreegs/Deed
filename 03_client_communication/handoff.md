# Client Communication — Handoff

## What the Client Communication Specialist Receives

The orchestrator passes a structured context object when routing here. All fields are required unless noted.

```
client_name:          [string]
intent:               "buy" | "sell"
communication_type:   [one of the template names — see rules.md for the full list]
assigned_agent:       [string — determines which voice profile to use]
deal_stage:           [current stage from lead_object.json]
contact_preference:   [from lead_object.contact_info.preferred_method — "email" | "phone" | "text"]
context:              [relevant excerpt from contact_log Running Timeline and Current State,
                       plus any specific detail triggering this communication —
                       e.g., property report summary, offer terms, inspection findings]
lead_object_path:     clients/open/[client_name]/lead_object.json
contact_log_path:     clients/open/[client_name]/contact_log.md
```

The specialist reads the full lead object and the contact_log before drafting. The context field from the orchestrator is a starting point — the files are the authoritative source.

---

## What the Client Communication Specialist Produces

### 1. Email Draft

**Written to:** `clients/open/[client_name]/email_drafts/[communication_type]_[YYYYMMDD].md`

Each draft file contains:

```
To:       [client email from lead_object.contact_info.email]
Subject:  [per template subject line format]
---
[email body]
```

If `contact_preference` is "text" or "phone," add a note at the top of the draft:

```
[NOTE: Client prefers [text/phone]. Consider whether this message is better delivered
by call or text. Email copy follows for reference.]
```

Drafts are never sent automatically. The agent reviews and sends.

### 2. Updated contact_log.md

**Running Timeline entry:**
```
[date] — Client Communication — [communication_type] draft written for [client_name].
[1 sentence: what the draft covers and any notable framing decision.]
```

**Current State overwrite:**
```
Current stage:     [mirrors deal_stage — do not change it]
Last action:       [communication_type] draft written — awaiting agent review and send
Next action:       Agent to review draft at email_drafts/[communication_type]_[YYYYMMDD].md and send
Open questions:    [anything in the draft that the agent may want to adjust before sending, or "None"]
Deadline:          [any deadline referenced in the draft, or "None identified"]
```

---

## Template and Voice Profile Lookup

The specialist resolves both files before drafting:

| Field | File path |
|---|---|
| Voice profile | `03_client_communication/voice_profiles/[assigned_agent].md` |
| Template | `03_client_communication/templates/[communication_type].md` |

If either file is missing, stop and alert the orchestrator before drafting.

---

## What the Orchestrator Does with the Output

After the specialist returns:

1. Confirms the draft file was written to `email_drafts/`.
2. Confirms `contact_log.md` was updated.
3. Surfaces the draft to the agent with the human gate: *"Here's the draft. Do you want to send this?"*

No email is sent without agent confirmation.
