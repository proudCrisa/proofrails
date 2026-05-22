# Concepts

How the harness is structured and why each part exists.

## Architecture

```text
harness/
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
├── .agentic/          # Harness workspace directory
│   ├── changes/       # Change proposals: proposal, design, tasks, specs
│   └── runs/          # Review reports and verification evidence
├── planwithfile/      # Working directory for active changes
├── .evidence/         # Immutable evidence records
└── .openspec/         # Optional: OpenSpec-managed spec lifecycle
```

## The state machine

The harness models AI-assisted development as a 12-stage state machine:

```
Intake -> Probe -> Mode -> Discovery -> Spec -> Challenge
  -> Approve -> Apply -> Review -> Verify -> Archive
```

Each stage produces artifacts on disk. No stage is skipped. Stages can loop back (e.g., review finds spec drift -> return to Spec).

### Stage artifacts

| Stage | Artifact(s) | Reversible? |
|---|---|---|
| Intake | `planwithfile/<id>/brief.md` | Yes, update any time |
| Probe | (terminal output, classified tools table) | N/A |
| Mode | `planwithfile/<id>/mode.md` | Yes, with confirmation |
| Discovery | `planwithfile/<id>/findings.md` | Yes, append-only preferred |
| Spec | `proposal.md`, `design.md`, `tasks.md`, `specs/` | Yes, before approval |
| Challenge | `planwithfile/<id>/decisions.md` | Yes, with reasoning |
| Approval | (user confirmation, no file) | No — gate is passed |
| Apply | `planwithfile/<id>/progress.md`, `evidence.md` | Yes, via git revert |
| Review | `.agentic/runs/<id>/review-report.md` | Yes, re-review |
| Verify | `evidence.md` (updated) | No — evidence is immutable |
| Archive | `.openspec/` or `.agentic/changes/<id>/` | No — terminal state |

## Modes

### Greenfield

New project, new repository, new module, new capability. The harness enforces:

- Product/design/technical docs or structured interview before spec.
- Architecture decisions documented in design.md.
- Vertical slice planning — what is the smallest complete thing to build first?

### Brownfield

Bug fix, refactor, performance, migration, legacy modernization. The harness enforces:

- Read-only discovery before any edit.
- Characterization tests or golden evidence before changes.
- Blast radius analysis — what else could break?
- Minimal change surface — touch only what must change.

### Hybrid

New subsystem inside an existing project. Combines:

- Greenfield spec for the new subsystem.
- Brownfield discovery for the existing integration points.
- Explicit interface contracts between old and new.

### Harness-bootstrap

Setting up the harness itself. Creates:

- Project instruction files (CLAUDE.md, AGENTS.md).
- Directory structure (.agentic, planwithfile, .evidence).
- No business code is touched.

## Tools and fallbacks

The harness detects available tools and adapts:

| Tool detected | Used for | Fallback if missing |
|---|---|---|
| git | Version control, diff, log | Required — no fallback |
| gstack | Planning/review/QA/challenge skills | Built-in workflow gates |
| OpenSpec CLI | Spec lifecycle management | `.agentic/changes/` directory |
| Superpowers | TDD and execution discipline | Emulated rules in harness |
| GitNexus | Code graph queries | `grep -r` / `find` |
| gbrain | Persistent memory | Works fine without it |

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

Evidence is the harness's answer to "trust but verify." Every verification gate produces or references evidence:

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
