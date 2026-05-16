# Deed

A file-based AI operating system for a real estate team. Handles lead intake, property research, client communication, and transaction coordination — all tracked in files that any agent can pick up mid-deal without a handoff call.

## Table of Contents

- [Getting Started](#getting-started)
- [Background](#background)
- [Usage](#usage)
  - [Starting a session](#starting-a-session)
  - [Slash commands](#slash-commands)
  - [Worked example](#worked-example)
- [Folder structure](#folder-structure)
- [Field reference](#field-reference)
- [How specialists work](#how-specialists-work)
- [Contributing](#contributing)
- [Future Features](#future-features)

## Getting Started

This section is for agents joining the team for the first time. Complete these steps before your first active session.

### Step 1 — Set up your voice profile

The system drafts emails in your voice. It will not produce any email draft without a voice profile on file. This is a hard requirement — there is no generic fallback.

Your profile lives in `03_client_communication/voice_profiles/[your_name].md`. Diana, Sarah, Michael, and Rob already have profiles. If you are a new agent:

1. Open `03_client_communication/voice_profiles/_template.md`.
2. Copy it to a new file named `[your_first_name].md` in the same folder.
3. Fill in every section — tone, openings, sign-off style, phrases you use, things you avoid, and two or three example sentences written in your voice.

The more specific you are, the more accurate the drafts will be. Spend 10–15 minutes on it. You only do this once.

### Step 2 — Start your first session

Open Claude Code at the project root. The first thing you say should identify yourself:

> "This is [your name]."

The system will run your personal briefing — a filtered view of only your open cases, sorted by urgency. If you have no open cases yet, it will say so and stop.

You do not need to do anything else to start. The system knows your cases, your stage, and what is due.

### Step 3 — Add your first lead

When a new lead comes in, just describe them in plain English:

> "I just got a new lead — [name], buyer, looking in [area], budget around $[X]. Their email is [email]."

The system will create the client folder, fill in everything it can from your message, and ask for anything missing. From that point, it routes automatically.

For sellers, the same approach works:

> "New seller lead — [name], wants to list their home at [address]. They're thinking $[price]."

### Step 4 — Learn the commands

Once you have active cases, these are the commands you will use most:

| Command | When to use it |
|---|---|
| `/status` | Morning briefing — full team view of every open case |
| `/shortlist [client]` | Before a showing — comparison of all properties a buyer is tracking |
| `/attach-doc [client] [doc_type] [filename]` | When a document arrives — drop it in `inbox/` first, then run this |
| `/dd-status [client]` | After going under contract — tracks every due diligence item and deadline |
| `/offer [client] [address] [action] [price?]` | To submit, accept, reject, or counter an offer |
| `/close [client]` | When a deal is done — runs the closing checklist and archives the file |

### Step 5 — Follow a deal through (worked example)

See the [Worked example](#worked-example) below for a full walkthrough of a buyer lead from intake to drafted first contact email. It covers what the system does automatically and where it pauses for your input.

---

## Background

Deed is built for real estate teams running multiple deal streams in parallel. The system replaces ad-hoc notes and verbal handoffs with a consistent file structure: every deal lives in a client folder, every action appends to a contact log, and any agent can pick up any deal by reading two files.

Claude Code reads the files, runs the relevant specialist, and writes output back to the same folder. The agents interact in plain English. The system handles routing, file management, and stage tracking.

## Usage

### Starting a session

Open Claude Code at the project root. The system identifies which agent the session is for, then runs a personal briefing filtered to that agent's open cases. The briefing surfaces anything urgent or overdue.

If nothing is flagged, the response is one line: "All your open cases are current."

### Slash commands

| Command | What it does |
|---|---|
| `/status` | Full team briefing — all open cases across all agents, sorted by urgency |
| `/shortlist [client]` | Buyer property comparison table across all properties in their active search |
| `/attach-doc [client] [doc_type] [filename]` | Takes a document from `inbox/`, renames it to [client name]-[report type], moves to the clients's folder, sets the due diligence flag, routes to TC |
| `/dd-status [client]` | Due diligence snapshot — what is complete, what is at risk, days to close |
| `/offer [client] [address] [action] [price?]` | Manages offer state: submit, accept, reject, or counter |
| `/close [client]` | Closes a deal — verifies all DD flags, archives the folder, updates the registry |

For `/attach-doc`: drop the file into `inbox/` first, then run the command with the filename.

### Email drafts

The communication specialist drafts emails but never sends them. Every draft is saved to the client's `email_drafts/` folder. The agent reviews and sends manually. A voice profile must exist for the assigned agent before any draft can be produced — the system will not fall back to a generic voice.

### Worked example

An agent opens Claude Code on a Tuesday morning and says:

> "I just got a new lead — Kevin Marsh, buyer, looking in 78704, budget around $550K. His email is kevin@example.com and he mentioned he's pre-approved."

The system creates Kevin's client folder, copies the lead object template, pre-populates every field it can from that message, and routes to the lead qualifier. The qualifier asks only for what is missing — timeline, constraints, any specific addresses. Once qualification is complete, it auto-routes to property research. When research is done:

> "Research complete for 2847 South Lamar. Do you want to draft a first contact email?"

The agent confirms. The communication specialist drafts the email in the agent's voice using the `first_contact` template. The session ends. Kevin's folder now has a lead object, a property report, a drafted email, and a contact log with a full timeline. `active_cases.md` has a new row.

Next session — any agent on the team — opens Claude Code, sees Kevin in the briefing, and knows exactly where things stand.

## Folder structure

```
deed/
├── CLAUDE.md                      — session startup behavior, slash commands, system overview
├── README.md                      — this file
├── active_cases.md                — one row per open deal; the system's live index
├── inbox/                         — drop zone for /attach-doc
├── clients/
│   ├── client_registry.md         — permanent record of every client, open and closed
│   ├── open/
│   │   └── [client_name]/
│   │       ├── lead_object.json   — client profile and deal state
│   │       ├── contact_log.md     — running timeline, current state, document checklist
│   │       ├── email_drafts/
│   │       ├── cma_report.md      — sellers only
│   │       └── properties/        — buyers only
│   │           └── [address]/
│   │               ├── property_object.json
│   │               └── property_report.md
│   └── closed/                    — archived after close; never deleted
├── 00_orchestrator/               — routing logic, stage gates, document handling rules
├── 01_lead_qualifier/             — intake and qualification
├── 02_property_research/          — property reports and CMA for sellers
│   └── community_notes.md         — agent-maintained local knowledge: neighborhoods, what to avoid, insider context
├── 03_client_communication/       — email drafts, templates, agent voice profiles
│   ├── templates/                 — one file per message type
│   └── voice_profiles/            — one file per agent; required before any draft can be produced
└── 04_transaction_coordinator/    — activates at under_contract; sets dates, builds deadline map, leads every interaction with proactive status
```

Each specialist folder contains four files: `identity.md`, `rules.md`, `handoff.md`, and `examples.md`.

## Field reference

The program uses 2 .json files. One to track the client and the status of the sale/purchase. The other handles all the information about the property. 

### Lead object

**`stage`** — owned exclusively by the orchestrator; specialists never write it directly.

Buyer track:
```
qualifying → qualified → property_search → offer_pending → under_contract → due_diligence → clear_to_close → closing → closed
```
Seller track:
```
qualifying → qualified → listing_prep → active_listing → offer_received → under_contract → due_diligence → clear_to_close → closing → closed
```

Before a new lead is qualified, the orchestrator checks `client_registry.md` for a matching email or phone number. If a match is found, the returning client's history is surfaced and the existing record is reused rather than creating a duplicate.

**`status`**

| Value | Meaning |
|---|---|
| `pending_info` | Qualification incomplete — follow-up needed before the deal can progress |
| `qualified` | All required fields present; deal is active and moving |
| `cold` | Lead not viable — unresponsive, wrong fit, or explicitly not interested |

**`intent`**

| Value | Meaning |
|---|---|
| `buy` | Buyer lead |
| `sell` | Seller lead |

**`pre_approved`**

| Value | Meaning |
|---|---|
| `true` | Pre-approval letter in hand |
| `"pending"` | In process with lender, not yet complete |
| `false` | Not yet started |
| `null` | Not applicable (sellers) |

**`contact_info.preferred_method`**

| Value | Meaning |
|---|---|
| `email` | Prefers email contact |
| `phone` | Prefers phone calls |
| `text` | Prefers text messages |

**`lead_source`**

| Value | Meaning |
|---|---|
| `zillow` | Zillow inquiry |
| `realtor_com` | Realtor.com inquiry |
| `referral` | Referred by another client or contact |
| `open_house` | Met at an open house |
| `website` | Came through the team website |
| `sign_call` | Called from a yard sign |
| `cold_call` | Outbound cold call |
| `repeat_client` | Has worked with the team before |
| `other` | Any other source |

**`constraints.tags`** — only confirmed-true tags are stored; never record a tag as false.

| Tag | Meaning |
|---|---|
| `must_sell_first` | Cannot buy until their current home sells |
| `cash_buyer` | Purchasing without a mortgage |
| `first_time_buyer` | No prior home purchase |
| `relocation` | Moving due to job or life change |
| `lease_expiring` | Rental lease ending, creating urgency |
| `investment_property` | Buying as an investment, not primary residence |
| `1031_exchange` | Proceeds from a prior sale must be reinvested |
| `divorce_sale` | Sale driven by divorce proceedings |
| `estate_sale` | Sale of a deceased person's property |

**`due_diligence` flags** — all must be `true` before `/close` will proceed.

| Flag | Meaning when `true` |
|---|---|
| `inspection` | Inspection report received |
| `appraisal` | Appraisal complete |
| `loan_approval` | Loan formally approved by lender |
| `title_search` | Title search clear |
| `contingencies_released` | All contingencies released |

---

### Property object

**`status`**

| Value | Meaning |
|---|---|
| `active` | Under active consideration by the buyer |
| `offer_pending` | Offer submitted, awaiting seller response |
| `offer_rejected` | Offer was rejected |
| `removed` | Buyer passed without making an offer |
| `accepted` | Offer accepted; property is under contract |

**`research_complete`**

| Value | Meaning |
|---|---|
| `false` | Property research not yet run |
| `true` | Research specialist has completed the report |

## How specialists work

No specialist talks directly to another. The flow is always:

**Agent → Orchestrator → Specialist → files → Orchestrator → next step**

Each specialist reads the client's lead object and contact log, does its work, writes output to the client folder, appends a Running Timeline entry, and returns control to the orchestrator.

Key rules:

- **The orchestrator routes everything.** Specialists never route each other.
- **The agent's word is accepted.** If you say "I have Susan's inspection report," the flag is set. Verification is your responsibility.
- **Templates are always copied, never reconstructed.** Lead and property objects are created from the templates in `00_orchestrator/`.
- **External actions are gated.** File moves, email sends, and document renames require confirmation. Internal file updates do not.
- **Folders are never deleted.** Closed and cold deals move to `clients/closed/`. The record always exists.

## Contributing

### Adding a new agent

1. Copy `03_client_communication/voice_profiles/_template.md` to `[agent_name].md` in the same folder.
2. Fill in the voice profile — tone, signature phrases, what to avoid, sample sentences.
3. The new agent can start taking sessions immediately.

No other configuration needed.

### Adding community knowledge

Open `02_property_research/community_notes.md`, find the right city section (or add one), add a neighborhood heading with zip codes, and write what you know. No special format required. This is the local knowledge the system uses to give clients honest, street-level context that won't appear in any listing.

### Adding a message template

1. Copy `03_client_communication/templates/_template.md` to a new file named for the message type.
2. Fill in the structure and any variable placeholders.
3. Reference the new template by filename when asking the communication specialist to draft.

## Future Features

- **Property image extraction** — automatically pull the primary listing image from a property's listing URL during research and store it alongside the property report.
- **Property report PDF export** — convert `property_report.md` into a fully formatted, client-ready PDF that agents can send directly without additional formatting work.
- **GUI interface** — a graphical front end for agents who prefer not to work directly in Claude Code; would surface case status, slash commands, and client folders through a point-and-click interface.
