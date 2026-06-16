# Concepts

How ProofRails is structured and why each part exists.

## Architecture

```text
proofrails/
├── skill/             # The main SKILL.md that orchestrates everything
├── scripts/           # POSIX shell scripts for setup and validation
├── templates/         # Reference templates users can customize
├── docs/              # Documentation in EN/ZH/FR
├── examples/          # Worked examples for common scenarios
└── .evidence/         # Implementation evidence (this project's own evidence)
```

### In your project

```text
your-project/
├── CLAUDE.md          # AI agent project instructions (source of truth)
├── AGENTS.md          # Multi-agent collaboration rules
├── .proofrails/          # ProofRails workspace directory (bootstrap fallback only)
│   ├── changes/       # Bootstrap-mode change drafts (when openspec/ is not yet initialized)
│   └── runs/          # Review reports and verification evidence
├── planwithfile/      # Working directory for active changes
│   └── <id>/
│       ├── brief.md
│       ├── findings.md
│       ├── capability-map.md   # mandatory before drafting any change spec
│       └── …
├── .evidence/         # Immutable evidence records
└── openspec/          # Required: Fission-AI/OpenSpec layout
    ├── changes/<id>/  # proposal, design, tasks, delta specs
    └── specs/<cap>/   # baseline capability specs (SSOT)
```

## The state machine

ProofRails models AI-assisted development as an 11-stage state machine:

```
Document Intake -> Probe -> Mode Detection -> Discovery -> Spec -> Challenge
  -> Approval -> Apply -> Review -> Verify -> Archive
```

Each stage produces artifacts on disk. No stage is skipped. Stages can loop back (e.g., review finds spec drift -> return to Spec).

### Stage artifacts

| Stage | Artifact(s) | Reversible? |
|---|---|---|
| Intake | `planwithfile/<id>/brief.md` | Yes, update any time |
| Probe | (terminal output, classified tools table) | N/A |
| Mode | `planwithfile/<id>/mode.md` | Yes, with confirmation |
| Discovery | `planwithfile/<id>/findings.md` | Yes, append-only preferred |
| Capability map | `planwithfile/<id>/capability-map.md` | Yes, before drafting specs |
| Spec | `openspec/changes/<id>/{proposal,design,tasks}.md`, delta `specs/<cap>/spec.md`, plus baseline `openspec/specs/<cap>/spec.md` if initialized this run | Yes, before approval |
| Challenge | `planwithfile/<id>/decisions.md` | Yes, with reasoning |
| Approval | (user confirmation, no file) | No — gate is passed |
| Apply | `planwithfile/<id>/progress.md`, `evidence.md` | Yes, via git revert |
| Review | `.proofrails/runs/<id>/review-report.md` | Yes, re-review |
| Verify | `evidence.md` (updated) | No — evidence is immutable |
| Archive | `openspec/changes/archive/<dated-id>/` (Fission-AI) or `.proofrails/changes/<id>/` (bootstrap fallback) | No — terminal state |

## Modes

### Greenfield

New project, new repository, new module, new capability. ProofRails enforces:

- Product/design/technical docs or structured interview before spec.
- Architecture decisions documented in design.md.
- Vertical slice planning — what is the smallest complete thing to build first?

### Brownfield

Bug fix, refactor, performance, migration, legacy modernization. ProofRails enforces:

- Read-only discovery before any edit.
- Characterization tests or golden evidence before changes.
- Blast radius analysis — what else could break?
- Minimal change surface — touch only what must change.

### Hybrid

New subsystem inside an existing project. Combines:

- Greenfield spec for the new subsystem.
- Brownfield discovery for the existing integration points.
- Explicit interface contracts between old and new.

### ProofRails-bootstrap

Setting up ProofRails itself. Creates:

- Project instruction files (CLAUDE.md, AGENTS.md).
- Directory structure (.proofrails, planwithfile, .evidence).
- No business code is touched.

## Tools and fallbacks

ProofRails detects available tools and adapts. The three-pack is required; everything else is optional.

| Tool | Status | Used for | If missing |
|---|---|---|---|
| git | required | Version control, diff, log | exit |
| OpenSpec (Fission-AI) | **required** | Spec lifecycle (`openspec/changes/`, `openspec/specs/`) | BLOCKED — `npm install -g @fission-ai/openspec@latest` |
| gstack | **required** | Planning/review/QA/challenge skills | BLOCKED — install per gstack docs |
| Superpowers | **required** | TDD and execution discipline | BLOCKED — install Superpowers skill pack |
| CodeGraph | optional | Code graph queries via `codegraph` CLI and `codegraph_*` MCP tools | `grep -r` / `find` |
| gbrain | optional | Persistent memory | Works fine without it |

## Risk boundaries

Risk boundaries define contracts that require explicit user approval before modification. They are documented in the **Risk boundaries** section of `CLAUDE.md` and include:

```markdown
# Risk Boundaries

## Public APIs
- `POST /api/v1/users` — external caller contract
- `GET /api/v1/orders` — mobile app dependency

## Data schemas
- `users` table — shared with analytics pipeline
- `orders` table — compliance retention policy applies

## Message queues
- `order.events` topic — consumed by billing service
- `user.actions` topic — consumed by recommendation engine

## Auth
- OAuth2 token validation — must maintain backward compatibility
- Session cookie format — shared with legacy frontend

## Deployment
- `docker-compose.prod.yml` — production topology
- `terraform/` — infrastructure as code
```

## Evidence

Evidence is ProofRails's answer to "trust but verify." Every verification gate produces or references evidence:

- **Build output** — stdout/stderr from build commands.
- **Test results** — pass/fail counts, coverage reports.
- **Lint/typecheck output** — zero warnings or documented exceptions.
- **Runtime smoke** — app starts, API returns 200, key flow works.
- **Manual evidence** — screenshot, log excerpt, user confirmation (when automation is impossible).

Evidence is written to `.evidence/` (immutable, append-only) or `planwithfile/<id>/evidence.md` (active change).

## Archive

Archive is a **terminal state**. After a change is archived:

- Its spec and evidence are frozen.
- Do not patch the same change in place.
- A follow-up change is a **new** change with its own change-id.
- This prevents scope creep and keeps each change's evidence chain intact.
