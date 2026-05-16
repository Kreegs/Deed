# Deed

A file-based AI operating system for a real estate team. Handles lead intake, property research, client communication, and transaction coordination — all tracked in files that any agent can pick up mid-deal without a handoff call.

## Table of Contents

- [Background](#background)
- [Usage](#usage)
  - [Starting a session](#starting-a-session)
  - [Slash commands](#slash-commands)
  - [Worked example](#worked-example)
- [Folder structure](#folder-structure)
- [Deal stages](#deal-stages)
- [How specialists work](#how-specialists-work)
- [Contributing](#contributing)
- [Future Features](#future-features)

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

## Deal stages

```
qualifying → qualified → property_search → offer_pending → under_contract
                      ↘ listing_prep ↗            → due_diligence → clear_to_close → closing → closed
```

Buyers enter at `qualifying`. Sellers enter at `listing_prep`. The orchestrator owns all stage transitions — specialists never change the stage themselves.

Before a new lead is qualified, the orchestrator checks `client_registry.md` for a matching email or phone number. If a match is found, the returning client's history is surfaced and the existing record is reused rather than creating a duplicate.

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
