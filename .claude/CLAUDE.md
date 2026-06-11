# OLIVIA — Claude Code Project Context

OLIVIA is a robotic arm system that manipulates monitor arms (pan, tilt, zoom, scope, raise, drop) for users. The goal is a simple, commercially available product. The stack is AI-heavy, robotics-first, and edge-deployed.

**Phase:** Greenfield. Solo build until a working physical prototype is in place, then a small team joins.

**Authoritative plan:** `Claude/OLIVIA_Plan.md` — single live planning doc. Always check before picking up new work.

# Project Olivia: System Architecture & AI Governance

## System Context
- **Project Goal:** Agentic, multi-limb robotic intelligence (OLIVIA).
- **Primary Stack:** Linux WSL, Python 3.11+, PyTorch, Ollama backend.
- **Memory Layer:** `./Obsidian/` (Local-only, air-gapped wiki framework).

## AI Memory Layer Architecture
The `./Obsidian` folder is structured as an AI-native Second Brain. 
When analyzing requirements, look up definitions and decisions here FIRST before asking the human operator.

### Folder Mapping & Strict Scope
- `01_Architecture/`: Structural definitions, system topology, data schema. Read-only for AI unless explicitly instructed.
- `02_Sessions/`: Chronological sequence of engineering execution. Format: `YYYY-MM-DD-session-title.md`. AI can append execution data here.
- `03_Reference/`: Fixed specifications, math formulas, API specs. Static reference blocks.
- `04_RAG_Staging/`: Temporary bucket for processing raw logs or external sources.

### Metadata Rules (YAML Frontmatter)
Every note created or modified by an AI must contain exactly this frontmatter block:
---
type: [architecture | session | reference | schema]
status: [draft | verified | deprecated]
tags: [robotics, kinematics, simulation, infrastructure]
last_audited: YYYY-MM-DD
---

### Navigation Protocol
- Follow `[[wikilinks]]` recursively to discover connected dependencies.
- Never write redundant information; link to existing atomic concept notes.

OLIVIA/
├── env/                   # Python Virtual Environment (Ignored by Git)
├── src/                   # Active application source code
├── CLAUDE.md              # Global AI behavior guide and vault orchestrator
└── Obsidian/              # Your Core Knowledge Vault
    ├── 01_Architecture/   # The "Source of Truth" (System layouts, wiring diagrams)
    ├── 02_Sessions/       # The "Audit Trail" (What happened, when, and why)
    ├── 03_Reference/      # The "Constants" (Math proofs, external hardware datasheets)
    └── 04_RAG_Staging/    # The "Buffer" (Raw terminal logs, scraped API docs)
---

## Stack

- **Computer Vision:** YOLO / Ultralytics, SOD
- **Simulation:** NVIDIA Isaac Sim (sim-to-real validation before touching hardware)
- **AI Inference:** NVIDIA NIMs, Nemotron
- **Edge Deployment:** Jetson Orin Nano (inverse kinematics, motion planning, on-device inference)
- **Agentic Framework:** GSTACK (role structure, SDLC enforcement, adversarial review, slash commands) + RUFLO (runtime orchestration, Graph RAG memory, edge pipelines) — to be integrated
- **Project Management:** Obsidian Project Planner (BRAT + obsidian-project-planner plugins installed in vault at `ObsidianOlivia/`)

---

## Safety Rails (non-negotiable)

These rules are enforced by hooks in `.claude/settings.json`. If a hook blocks something, stop and ask — do not work around it.

- **Never touch** `.env`, `secrets/*`, or any file matching `*_token.*` / `*_key.*` / `*credentials*`.
- **Never commit or push secrets, API keys, or credentials** for any reason. All secrets are file-based and gitignore-controlled. Nothing private ever reaches a remote.
- **Never commit** `env/`, `__pycache__/`, `.chroma/`, model weights, or any secrets file.
- **Physical safety first.** Before modifying motion planning, IK solvers, or arm control logic: read the current plan doc, discuss the approach, record the decision in the session log.

---

## Agentic DevSecOps Scope

Decisions made 2026-06-11. These define what the agentic team does and does not handle.

**In scope:**
- **Secrets detection** — no credentials ever reach GitHub; gitignore-enforced
- **Dependency auditing** — third-party libraries (Ultralytics, NVIDIA SDKs, etc.) monitored for known vulnerabilities and suspicious updates
- **Incident triage** — when something fails in Isaac Sim or on the physical arm, automatically collect logs, sensor data, and error context to speed up debugging
- **Compliance reporting** — audit trails and documentation for commercial readiness, certifications, and future team/investor visibility

**Explicitly out of scope:**
- SAST / DAST (no automated code scanning)
- PR security review (no gating on merge)

---

## GSTACK Role Assignments

Active roles for the solo prototype phase (selected from GSTACK's 23):

1. **Engineering Manager** — SDLC discipline, task sequencing, code quality gates
2. **Architecture Reviewer** — adversarial pressure-testing of system design; flags sim-to-real gaps before hardware contact
3. **QA Engineer** — Isaac Sim validation runs; regression checks on IK and CV pipeline behavior
4. **Release Manager** — compliance docs, audit trail generation, end-of-session logs, dependency audit reports
5. **Security Auditor** — secrets detection enforcement, dependency vulnerability triage

Expand to additional roles (e.g., Product Strategy, Lead Designer) when the team grows.

**RUFLO** handles: persistent memory across sessions (Graph RAG over simulation runs, failure modes, IK history), edge pipeline deployment to the Jetson Orin Nano, and swarm consensus for dependency auditing. Integrate RUFLO once Isaac Sim is generating data worth remembering.

---

## Session Bootstrap

**Do this automatically at the start of every session. The user should not have to ask.**

Read in this order before responding to the first substantive prompt:

1. **`Claude/Projects/OLIVIA/Tasks/`** — live priority queue (Obsidian Project Planner). One markdown file per task with frontmatter (`status`, `priority`, `startDate`, `dueDate`, `tags`, `dependencies`). Find active work: `grep -lE '^status: (Next|In Progress)' Claude/Projects/OLIVIA/Tasks/*.md`
2. **`Claude/OLIVIA_Plan.md`** — narrative companion: version rationale, constraints, acceptance criteria. Use for **why**; use Tasks for **what's next**.
3. **Most recent `Claude/YYYY-MM-DD_*.md` session log** — concrete file changes, decisions, follow-ups, and "Tomorrow's natural next pieces" subsection.

After bootstrap, ask what the user wants to work on, or pick up from "Tomorrow's natural next pieces" if the user says "continue from yesterday."

---

## Context Navigation

Use this order for deeper lookups within a session:

1. `Claude/Projects/OLIVIA/Tasks/` — re-check for status changes
2. `Claude/OLIVIA_Plan.md` — version and rationale context
3. `Claude/YYYY-MM-DD_*.md` — most recent session log, then prior logs chronologically
4. Scoped `CLAUDE.md` files in subpackages if present
5. Source code — only when the layers above don't answer the question

Re-read the full repo only after structural changes (new module, new hardware interface, schema change, new agent).

---

## gstack

gstack is installed at `~/.claude/skills/gstack`. Use the `/browse` skill from gstack for all web browsing — never use `mcp__claude-in-chrome__*` tools.

**Active roles for OLIVIA (solo prototype phase):**

| Role | Skill | Purpose |
|------|-------|---------|
| Architecture Reviewer | `/office-hours`, `/plan-eng-review` | Adversarial pressure-testing of system design; flags sim-to-real gaps before hardware contact |
| Engineering Manager | `/plan-eng-review`, `/careful` | SDLC discipline, task sequencing, code quality gates |
| QA Engineer | `/qa`, `/qa-only` | Isaac Sim validation runs; regression checks on IK and CV pipeline |
| Release Manager | `/ship`, `/document-release` | Compliance docs, audit trail generation, dependency audit reports, end-of-session logs |
| Security Auditor | `/cso` | OWASP + STRIDE threat model; secrets detection enforcement; dependency vulnerability triage |

**Full skill list:** `/office-hours`, `/plan-ceo-review`, `/plan-eng-review`, `/plan-design-review`, `/design-consultation`, `/design-shotgun`, `/design-html`, `/review`, `/ship`, `/land-and-deploy`, `/canary`, `/benchmark`, `/browse`, `/connect-chrome`, `/qa`, `/qa-only`, `/design-review`, `/setup-browser-cookies`, `/setup-deploy`, `/setup-gbrain`, `/retro`, `/investigate`, `/document-release`, `/document-generate`, `/codex`, `/cso`, `/autoplan`, `/plan-devex-review`, `/devex-review`, `/careful`, `/freeze`, `/guard`, `/unfreeze`, `/gstack-upgrade`, `/learn`

---

## Commands

- `/session-log` — write `Claude/YYYY-MM-DD_<topic>.md` with full session record: decisions made, files changed, reasoning, follow-ups, and "Tomorrow's natural next pieces"
- `/dependency-audit` — run dependency vulnerability check across Python env and flag any issues
- `/incident-triage` — collect and summarize logs, sensor data, and error context from a sim or hardware failure
- `/compliance-report` — generate audit trail document from session logs and git history

---

## Conventions

- **Python style:** PEP 8, snake_case for functions/vars, PascalCase for classes, UPPER_SNAKE for constants
- **Imports:** stdlib → third-party → local
- **Error handling:** `logger.warning/debug` for internal; user-facing errors should be plain English a high schooler can understand
- **LLM outputs:** Any LLM-generated analysis or explanation must be in simple plain English. Longer explanations stored in logs; summaries used for display
- **New modules:** Discuss approach before implementing non-trivial changes
- **Git:** Commit to `master`, push to `origin/master`. **Commits happen only at end-of-session and only when the user explicitly requests one.** Never commit mid-session, never commit proactively, never suggest a commit unprompted. Pushes follow the same rule.

---

## Do

- Read `Claude/OLIVIA_Plan.md` and the Obsidian Project Planner task queue before picking up work
- Confirm active task and phase with the user at session start if unclear
- Read existing code before modifying it; follow Context Navigation order
- Discuss approach before making non-trivial changes
- Run `/session-log` at the end of every substantive session
- Update session notes with exact responses, recommendations, and decisions from the session — this is the history record and context for future sessions
- Keep all secrets in files, gitignore-controlled, never in code or environment variables passed to remote systems

## Do NOT

- Commit or push without explicit user request — default is always **wait**
- Suggest a commit unprompted, slip a `git commit` into a multi-step plan, or checkpoint work without being asked
- Touch `.env`, `secrets/*`, `*_token.*`, `*_key.*`, or `*credentials*` files
- Add emoji unless explicitly requested
- Modify motion planning or IK code without reading the plan doc and discussing first
- Assume Isaac Sim results are sufficient — always flag when a behavior needs physical hardware validation

