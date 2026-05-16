# Diana Real Estate AI System

## Session Startup

At the start of every session, before doing anything else:

1. **Identify the agent.** If the opening message does not name an agent (e.g. "This is Sarah"), ask: "Which agent is this session for?" Wait for the answer before proceeding.

2. **Run a personal briefing.** Using the same logic as `/status`, check all open cases in `active_cases.md` — but show only the cases assigned to the identified agent. Use the same three-bucket output (Urgent / Watch / On Track) and the same stale thresholds and due-diligence deadline rules defined in `.claude/commands/status.md`.

   If the agent has no flagged cases, output one line: "All your open cases are current." and stop.

   If there are no open cases at all, output: "No open cases." and stop.

---

## Available Commands

| Command | What it does |
|---|---|
| `/status` | Full team briefing — all agents, all open cases |
| `/shortlist [client]` | Buyer property comparison across shortlisted properties |
| `/attach-doc [client] [doc_type] [filename]` | Intake a document from `inbox/` and route it to the right client folder |
| `/dd-status [client]` | Due diligence snapshot — deadlines, outstanding items |
| `/offer [client] [address] [action] [price?]` | Offer state management — submit, accept, reject, or counter |
| `/close [client]` | Deal closing workflow — DD gate check, archive, registry update |

---

## System Overview

This is a multi-agent operating system for a 4-person real estate team: Diana, Sarah, Michael, and Rob.

**Specialist modules** (read these to understand your role):

| Folder | Role |
|---|---|
| `00_orchestrator/` | Entry point, routing, file management |
| `01_lead_qualifier/` | Lead intake and qualification |
| `02_property_research/` | Property reports and CMA analysis |
| `03_client_communication/` | Email drafts in each agent's voice |
| `04_transaction_coordinator/` | Due diligence, deadlines, closing checklist |

Each specialist folder contains: `identity.md`, `rules.md`, `handoff.md`, `examples.md`.

**Key files:**

- `active_cases.md` — live index of all open deals
- `clients/open/[name]/lead_object.json` — client profile and deal state
- `clients/open/[name]/contact_log.md` — running timeline, next action, deadlines
- `clients/closed/` — archived deals (never deleted)
- `inbox/` — drop zone for `/attach-doc`
