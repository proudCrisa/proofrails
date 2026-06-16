---
name: proofrails
description: "Single-entry AI engineering guardrails for greenfield and brownfield projects. Detects missing tool prerequisites, gathers required product/design/technical inputs, creates durable specs/plans, gates implementation, verifies evidence, and archives learning."
version: "1.0.0"
author: "ProofRails Contributors"
license: "Apache-2.0"
metadata:
  hermes:
    tags:
      - proofrails
      - bootstrap
      - greenfield
      - brownfield
      - workflow
      - engineering
  related_skills:
    - office-hours
    - plan-ceo-review
    - plan-eng-review
    - plan-design-review
    - plan-devex-review
    - investigate
    - review
    - qa
    - test-driven-development
---

# ProofRails

Use this skill as the single entry point when a user wants to bootstrap ProofRails in a project, or run build/refactor/optimize/migrate/debug work through ProofRails.

Primary goal: lower the barrier to setting up reliable project guardrails. A user should be able to point at a project and get the missing rules, plans, specs, risk boundaries, verification commands, and evidence structure without manually choosing planning tools, spec formats, or review skills.

The user should not need to manually choose gstack, OpenSpec, Superpowers, planning files, or review skills. This skill orchestrates only what is needed.

## Invocation Contract

Invoke this skill with one complete task sentence. Include the target project path, goal, constraints, success criteria, and any docs/links when available.

Canonical command:

```text
/proofrails <target project> <goal> <constraints> <success criteria> <docs or links>
```

Complete examples:

```text
/proofrails in /home/user/projects/payment-service upgrade the auth framework. Docs: https://wiki.example.com/auth-migration. Start with read-only discovery, risk boundaries, upgrade plan, and verification approach. Do not modify business code yet. High-risk boundaries include dependency manifests, auth configuration, startup scripts, and deployment config. Success criteria: produce a planwithfile/ plan listing files to change, test commands, and rollback steps.
```

```text
/proofrails in /home/user/projects/web-app fix login audit log write failures. Do not touch deployment scripts or production config. Start with brownfield discovery: find entry points, call chain, existing tests, and minimal change surface. Wait for approval before editing code. Success criteria: new or reused regression tests, module tests pass, and evidence recorded.
```

```text
/proofrails in the current project generate a ProofRails environment. Create AI development workflow files only, no business code changes. Produce CLAUDE/AGENTS rules, planwithfile structure, .proofrails changes fallback, and ProofRails scripts. Confirm target directory before writing.
```

```text
/proofrails in /path/to/new-service start a new service project. Interview for product/design/engineering/operations inputs first. Generate proposal, design, tasks, and first vertical slice plan. Do not write business implementation yet. Success criteria: clear tech stack, interface boundaries, test strategy, run commands, deploy and rollback plan.
```

## Core Mission

Turn AI coding into a reproducible engineering state machine:

```text
Document Intake -> Probe -> Mode Detection -> Discovery -> Spec -> Challenge -> Approval -> Apply -> Review -> Verify -> Archive
```

The skill supports:

- **Greenfield**: new project, new repository, new module, new capability.
- **Brownfield**: bug fix, refactor, performance optimization, migration, legacy modernization.
- **Hybrid**: new subsystem inside an existing project.
- **ProofRails-bootstrap**: set up or validate ProofRails in a repository without implementing product/business code.

## Non-Negotiable Rules

1. **Single entry** — do not make the user pick other skills. If another skill is needed, invoke or instruct it from this workflow.
2. **No source edits before approval** — planning/spec artifacts are allowed; business/source code edits wait until the approval gate.
3. **Three-pack is required** — OpenSpec (Fission-AI flavor), Superpowers, and gstack must be installed before any non-bootstrap run. If any is missing, stop with `NEEDS_CONTEXT` and print the one-line install command. CodeGraph and gbrain remain optional with degraded fallbacks.
4. **Missing product/design/technical docs are not ignored** — if needed inputs are absent, drive an interview or invoke the appropriate planning skill to produce them.
5. **Spec before implementation** — every non-trivial change gets a written proposal, design, tasks, and acceptance scenarios.
6. **Discovery before editing** — brownfield work requires read-only discovery and behavior capture before modification.
7. **Evidence over claims** — completion requires test/build/review/runtime evidence, or an explicit user-accepted exception.
8. **Archive is a terminal state** — after archive, do not patch the same change in place. Start a new change.
9. **High-risk contracts require approval** — public APIs, data schema, message queues, auth, CI/CD, deployment, and production config require user confirmation.
10. **Use local authenticated browser for private project docs** — prefer the user's logged-in session for private project docs. Ask for authorization if cookie/browser tooling is needed.
11. **Test-driven implementation** — load the `test-driven-development` skill and follow RED-GREEN-REFACTOR. No production code without a failing test first. "When practical" is not an exception. If you cannot write a test, stop and ask the user.

## Stage 0 — Bootstrap and Tool Probe

Run lightweight probes first.

Recommended checks:

```bash
pwd
git rev-parse --show-toplevel 2>/dev/null || true
git status --short 2>/dev/null || true
git branch --show-current 2>/dev/null || true

# Required three-pack
# OpenSpec must be the Fission-AI flavor (proposal/design/tasks/specs lifecycle).
# Some users have a name-collision tool also called `openspec`; the help text
# distinguishes them.
if command -v openspec >/dev/null 2>&1; then
  openspec --help 2>&1 | grep -qiE 'spec-driven|fission|opsx|proposal' \
    && echo "openspec: fission-ai (ok)" \
    || echo "openspec: NAME COLLISION — found a different 'openspec' binary"
else
  echo "openspec: missing"
fi
test -f ~/.claude/skills/gstack/SKILL.md && echo "gstack: ok" || echo "gstack: missing"
test -d ~/.claude/skills/superpowers || test -d .superpowers-memory \
  && echo "superpowers: ok" || echo "superpowers: missing"

# Optional
command -v codegraph >/dev/null 2>&1 && echo "codegraph: ok" || echo "codegraph: missing"
test -d .codegraph && echo "codegraph index: ready" || echo "codegraph index: not initialized"
command -v gbrain >/dev/null 2>&1 && echo "gbrain: ok" || echo "gbrain: missing"
```

Classify tools:

| Tool | Status | Purpose | If missing |
|---|---|---|---|
| git | required | repo state, diff, log | exit |
| OpenSpec (Fission-AI) | **required** | spec lifecycle (`openspec/changes/<id>/`, `openspec/specs/<cap>/`) | BLOCKED — `npm install -g @fission-ai/openspec@latest` |
| gstack | **required** | planning, challenge, review, QA, ship skills | BLOCKED — install per https://github.com/gstack |
| Superpowers | **required** | TDD and execution discipline | BLOCKED — install Superpowers skill pack |
| CodeGraph | optional (recommended) | code graph queries via `codegraph` CLI and `codegraph_*` MCP tools | ask once: run `codegraph init -i`? else degrade to `grep`/`find` |
| gbrain | optional | persistent memory and code search | continue silently |
| browser/cookies | situational | authenticated project docs | use local browser session or skip |

### Three-pack gate

If any of OpenSpec / gstack / Superpowers is missing or wrong-flavor, stop the run with `NEEDS_CONTEXT` and print the install line. Do NOT proceed in a degraded mode for these three. The only exception is `proofrails-bootstrap` mode running on a brand-new repo where the user explicitly opts to set up ProofRails first and add the three-pack later — in which case use the `.proofrails/changes/` fallback and label every artifact `bootstrap-only`.

If the OpenSpec probe reports a name collision, surface it verbatim:

```text
Found a binary named `openspec` but it is NOT @fission-ai/openspec.
ProofRails expects the Fission-AI lifecycle CLI (proposal/design/tasks/specs).
Action: rename or uninstall the other tool, then `npm install -g @fission-ai/openspec@latest`.
```

### Environment authorization rule

If a *required* tool is absent, ask:

```text
ProofRails requires <tool> for <reason>. Install now with:
  <one-line install command>
Run it? (y/N)
```

If a *required* tool is missing and the user declines, exit `NEEDS_CONTEXT`. Do not silently fall back.

For *optional* tools (CodeGraph, gbrain, browser cookies) the legacy three-choice prompt applies:

1. Install/configure now.
2. Continue in degraded mode.
3. Stop and let the user set it up manually.

## Stage 1 — Intake Gate

Collect enough input to choose a mode and write a bounded brief.

Ask only what is missing. Do not ask for documents the user already provided.

Required intake fields:

```text
Goal
Non-goals
Expected users / callers
Success criteria
Risk tolerance
Rollback or stop condition
Deadline or urgency
Available product/design/technical docs
```

### Required document handling

For **greenfield** work, require one of:

- Product requirements document
- Design document
- Technical architecture document
- Issue/ticket/spec link
- User interview answers produced by this skill

If none exists, drive a structured interview before continuing.

Interview sections:

```text
Product: user, problem, target workflow, acceptance criteria
Design/UX: screens, APIs, CLI, data contracts, examples
Engineering: stack, deployment, data model, integrations, constraints
Operations: observability, rollout, rollback, security, ownership
```

For **brownfield** work, require or discover:

```text
Existing code entry points
Known failing behavior or target behavior
Relevant docs/specs/wiki
Existing tests or fixtures
Runtime/build/test commands
High-risk contracts
```

Output artifact:

```text
planwithfile/<change-id>/brief.md
```

## Stage 2 — Mode Detection

Choose:

```text
greenfield
brownfield
hybrid
proofrails-bootstrap
```

Rules:

- Empty/new repo or new product: greenfield.
- Existing production system change: brownfield.
- New capability inside existing system: hybrid.
- Request is to create, validate, or repair the AI development workflow itself: proofrails-bootstrap.

Write:

```text
planwithfile/<change-id>/mode.md
```

If mode affects gates materially, summarize and ask for confirmation.

## Stage 3 — Context Strategy

Do not load everything. Build a context plan.

Greenfield context:

```text
Product docs
Design docs
Technical docs
Target stack
Team conventions
Initial quality bar
```

Brownfield context:

```text
CLAUDE.md / AGENTS.md / OpenSpec
Code graph / CodeGraph (`codegraph_context`, `codegraph_trace`, `codegraph_callers`, `codegraph_callees`, `codegraph_impact`)
Code entry points
Tests and fixtures
Runtime config
External contracts
Risk boundaries
```

Hybrid context combines both.

Write:

```text
planwithfile/<change-id>/context-plan.md
planwithfile/<change-id>/findings.md
```

Allowed:

- Read files
- Query code graph tools
- Use read-only agents
- Open authenticated docs in browser

Forbidden:

- Edit source code

## Stage 4 — Read-Only Discovery

Greenfield discovery answers:

```text
What is the smallest complete vertical slice?
What architecture is boring and sufficient?
What tests and CI should exist from day one?
What docs/instructions must be created so future agents can work safely?
```

Brownfield discovery answers:

```text
Where is the current behavior implemented?
Which capability or capabilities does this change touch? (name them)
What calls it and what does it call?
What config/data contracts does it depend on?
What tests or fixtures exist?
What hidden compatibility risks exist?
What is the blast radius of changing it?
```

The capability question is mandatory. Names should match `openspec/specs/<capability>/` if any exist; otherwise propose canonical names that future specs can adopt.

For brownfield behavior changes, identify characterization strategy:

- Unit characterization test
- Golden input/output fixture
- Existing log/sample replay
- API snapshot
- Browser/user-flow evidence
- Manual evidence if automation is impossible

Write:

```text
planwithfile/<change-id>/findings.md
```

## Stage 5a — Capability Map

This stage is mandatory for greenfield, brownfield, and hybrid modes. It is skipped only in `proofrails-bootstrap` mode. Without an approved capability map, Stage 5b (Spec Draft) MUST NOT begin and Stage 7 (Approval Gate) MUST refuse to pass.

The capability map answers three questions before any spec is written:

1. Which capabilities does this change touch?
2. Does each capability already have a spec?
3. What is the delta type for each (ADDED / MODIFIED / REMOVED / RENAMED)?

### Procedure

For each affected capability identified in Stage 4:

```text
Look up openspec/specs/<capability>/spec.md
  ├─ EXISTS  → read it. Record current behavior summary. Mark delta type.
  └─ MISSING → ASK USER. Do not guess.
                 "No spec exists for <capability>. Options:
                   [Y] init a baseline spec from current code (recommended for brownfield)
                   [N] skip and record a reason (e.g. capability is not yet specified by team)
                   [C] cancel — capability list is wrong, return to Stage 4"
```

When the user answers Y for a missing spec:

- For brownfield: read current behavior from code, write a baseline `openspec/specs/<capability>/spec.md` capturing today's behavior. This is a *baseline*, not a delta. The actual change is then expressed as a delta in the change folder (Stage 5b).
- For greenfield: proceed to Stage 5b without a baseline; the capability is brand-new and the change folder's spec IS the first definition.

When the user answers N (skip), the reason is recorded in `capability-map.md` and surfaced again at the approval gate so the user re-confirms.

### Output

Write `planwithfile/<change-id>/capability-map.md`:

```markdown
# Capability Map: <change-id>

| Capability | Spec exists? | Delta | Note |
|---|---|---|---|
| <name>     | yes/no/just-initialized | ADDED/MODIFIED/REMOVED/RENAMED | <one line> |

## Skipped capabilities

<List capabilities the user chose to leave un-specified, with reason. Empty if none.>

## Baselines initialized this run

<List capabilities whose `openspec/specs/<cap>/spec.md` was written from current code. Empty if none.>
```

### Gate

Stage 5b cannot begin until `capability-map.md` exists and the user has confirmed it. Stage 7 must include the capability-map summary in the approval bundle.

## Stage 5b — Spec Draft

Create OpenSpec-style artifacts using the Fission-AI/OpenSpec layout.

If `openspec/` exists (Fission-AI/OpenSpec is initialized), use:

```text
openspec/changes/<change-id>/proposal.md
openspec/changes/<change-id>/design.md
openspec/changes/<change-id>/tasks.md
openspec/changes/<change-id>/specs/<capability>/spec.md
```

Capability spec.md files at `openspec/specs/<capability>/spec.md` are the SSOT (baselines). The change folder holds DELTAS only.

If `openspec/` does not exist and the user has confirmed three-pack-gated `proofrails-bootstrap` mode, use fallback:

```text
.proofrails/changes/<change-id>/proposal.md
.proofrails/changes/<change-id>/design.md
.proofrails/changes/<change-id>/tasks.md
.proofrails/changes/<change-id>/specs/<capability>/spec.md
```

This fallback is reserved for bootstrap-only validation runs. For real code work the three-pack gate (Stage 0) requires Fission-AI/OpenSpec to be installed and `openspec init` to have been run.

If the user's goal is to bootstrap a full ProofRails environment, propose creating `openspec/` (via `openspec init`) as part of the setup.

### Proposal template

```markdown
# Proposal

## Why

## What changes

## Non-goals

## Impact

## Risks

## Rollback
```

### Design template

```markdown
# Design

## Current state

## Target state

## Key decisions

## Alternatives rejected

## Data flow / control flow

## Risk matrix

## Verification strategy
```

### Tasks template

```markdown
# Tasks

- [ ] Discovery complete
- [ ] Spec scenarios approved
- [ ] Tests or characterization evidence added
- [ ] Implementation slice 1
- [ ] Review against spec
- [ ] Verification evidence captured
- [ ] Archive/update durable knowledge
```

### Spec scenario format

```markdown
## ADDED Requirements

### Requirement: <capability behavior>

#### Scenario: <scenario name>
- Given ...
- When ...
- Then ...
```

Use `ADDED`, `MODIFIED`, `REMOVED`, `RENAMED` style headings when compatible with the local OpenSpec tool. If the local OpenSpec convention differs, follow the local convention.

## Stage 6 — Challenge Gate

Challenge before implementation. Use four perspectives:

```text
Business / user value
Risk / failure / rollback
Implementation / simplicity / fit
Historical / rules / code graph conflicts
```

If gstack is available, use the relevant planning/review skill when it adds value:

- `/office-hours` for product/problem uncertainty.
- `/plan-ceo-review` for scope/strategy uncertainty.
- `/plan-eng-review` for architecture/test/performance uncertainty.
- `/plan-design-review` for UI/UX plans.
- `/plan-devex-review` for API/CLI/SDK/dev-facing work.

Do not force every gstack skill. Pick the smallest set that closes the actual uncertainty.

Record decisions:

```text
planwithfile/<change-id>/decisions.md
```

Gate:

- P0/P1 unresolved challenge findings block implementation.
- User must approve high-impact decisions.

## Stage 7 — Approval Gate

Before source edits, present:

```text
Mode
Scope
Non-goals
Affected capabilities + spec status (links capability-map.md)
Artifacts written
High-risk boundaries
Implementation slices
Verification plan
Rollback plan
Open decisions
```

Approval CANNOT pass without the capability-status item. If `capability-map.md` is missing, marked incomplete, or contains skipped capabilities the user has not re-confirmed, return to Stage 5a.

Ask for approval to proceed.

Allowed before approval:

- Spec/planning document edits
- Read-only discovery

Forbidden before approval:

- Source/business code edits
- Dependency changes
- CI/CD changes
- Commits/pushes/deploys

## Stage 8 — Execution Plan

Convert tasks into small slices.

Greenfield slices:

```text
Project skeleton
First vertical slice
Core tests
Edge cases
Observability
Docs/instructions
ProofRails scripts
```

Brownfield slices:

```text
Characterization tests or golden evidence
Seam/adapter
One caller/path migration
Behavior-preserving refactor
Performance or logic change
Old path cleanup
Docs/spec update
```

Rules:

- One slice should be reviewable.
- Avoid opportunistic cleanup.
- Keep rollback simple.
- Prefer branch-by-abstraction for large replacements.

Write/update:

```text
planwithfile/<change-id>/task_plan.md
```

## Stage 9 — Implementation Loop

**Mandatory: load the `test-driven-development` skill before entering this stage.** Follow its rules without exception. In particular, the full cycle for each task is:

```text
DESIGN → TEST CASES → IMPLEMENT (RED-GREEN-REFACTOR)
```

Do NOT merge the test-case-design step into implementation. Write a test case document first, then implement each case via RED-GREEN-REFACTOR.

For each task:

```text
Read task
Confirm exact files
Design test cases (write test case document covering the task)
For each test case:
  Write failing test (RED)
  Run test — verify it fails for the expected reason
  Write minimal code to pass (GREEN)
  Run test — verify it passes
  Refactor if needed (keep tests green)
Record evidence
Mark task complete only after evidence
```

**Hard gate — no production code without a failing test first.** "When practical" is not a valid exception. If you cannot write a test, stop and ask the user.

If Superpowers is installed, follow its relevant workflow principles:

- TDD (enforced, not optional).
- Systematic debugging for bugs.
- Verification before completion.
- Small tasks.
- Review before finishing.

If Superpowers is missing, emulate these rules.

Hard stops:

- Design no longer fits discovered facts.
- High-risk contract touched.
- Test failures are unexplained.
- Diff expands beyond approved scope.
- User decision required.

Write:

```text
planwithfile/<change-id>/progress.md
planwithfile/<change-id>/evidence.md
```

## Stage 10 — Review Against Spec

Review dimensions:

```text
Every scenario has implementation or explicit non-implementation reason
Changed files match plan
No unrelated refactor
Code graph changes reviewed
Tests/verification cover success and failure paths
Architecture boundaries preserved
Security/data contracts preserved
Rollback remains possible
```

Use tools when available:

```bash
git diff
```

If gstack `/review`, code-review, or Codex review is useful, invoke it or ask authorization for the external tool if missing.

Output:

```text
.proofrails/runs/<change-id>/review-report.md
```

If review finds spec drift:

1. If implementation is wrong: fix implementation.
2. If spec is wrong: return to Spec Draft and update spec first.
3. If scope changed: ask user approval.

## Stage 11 — Verification Gate

Verification is required before claiming done.

Greenfield verification:

```text
Build
Unit tests
Integration/smoke tests
Lint/typecheck where applicable
Run app or API smoke
Docs/examples if developer-facing
```

Brownfield verification:

```text
Characterization/regression tests
Targeted module tests
Build/package
Golden sample replay
Performance smoke for performance work
Runtime/manual evidence when automation is impossible
```

Evidence record:

```markdown
# Evidence

## Commands run

## Results

## Output summary

## Screenshots / artifacts

## Gaps

## User-accepted exceptions
```

Forbidden:

- Claiming success without evidence.
- Hiding failed commands.
- Using deployment as verification unless explicitly authorized.

## Stage 12 — Archive and Memory

After implementation and verification:

```text
Update OpenSpec SSOT or equivalent docs
Archive change if OpenSpec supports it
Update planning files
Update project instructions if durable rules changed
Re-index code graph if changed and user authorizes
Sync knowledge base if configured and user policy allows
```

Archive output:

```text
Archived spec/change
Final evidence
Lessons learned
Next recommended work
```

## ProofRails Bootstrap Mode

If the user asks to set up ProofRails in a repository, this skill should create or propose:

```text
CLAUDE.md / AGENTS.md project rules
.openspec or .proofrails/changes structure
planwithfile/ structure
.proofrails/runs/ structure
.evidence/ structure
proofrails-check script
proofrails-lint script
basic test/build command registry
skill routing notes
```

For a new project, require product/design/technical inputs first. If absent, interview the user or invoke planning skills to produce them.

For an existing project, run read-only discovery first and infer current build/test commands before writing ProofRails scripts.

## User Question Policy

Ask questions only when they change the result:

Ask when:

- Goal/scope is ambiguous.
- Product/design/technical docs are absent for greenfield work.
- Environment install/configuration is needed.
- High-risk contracts are touched.
- Verification cannot be automated.
- User approval gate is reached.

Do not ask when:

- A safe read-only probe can answer it.
- The repo already documents the answer.
- A missing optional tool has a safe degraded path and the user requested speed.

## Completion Status

End with one of:

```text
DONE — ProofRails stage completed with evidence.
DONE_WITH_CONCERNS — completed but with listed gaps.
BLOCKED — cannot continue; exact blocker and attempted actions listed.
NEEDS_CONTEXT — user input or document required.
```

## Implementation Notes for This Skill

This skill is intentionally a coordinator. It may invoke or instruct these tools/skills:

- `browse` / `setup-browser-cookies` for authenticated docs.
- `office-hours`, `plan-ceo-review`, `plan-eng-review`, `plan-design-review`, `plan-devex-review` for challenge gates.
- `investigate` for bugs/root cause.
- `review`, `code-review`, `codex` for review gates.
- `qa`, `qa-only` for runtime verification.
- `setup-gbrain`, `sync-gbrain` for durable memory/code search.
- OpenSpec CLI or local `.proofrails/changes` fallback for specs.
- CodeGraph CLI (`codegraph`) and `codegraph_*` MCP tools for code graph.

The user invokes only this skill. This skill decides which subordinate capability to use.

## Sources and Rationale

This workflow is based on cross-learning from:

- Anthropic effective agents: simple workflows, clear tools, evals, stopping conditions.
- Claude Code best practices: explore-plan-code, verification, skills, hooks, subagents, context management.
- OpenSpec: proposal/design/tasks/spec/archive lifecycle.
- Superpowers: TDD, systematic workflows, evidence over claims.
- gstack: Think -> Plan -> Build -> Review -> Test -> Ship -> Reflect.
- GitHub Copilot coding agent: task-to-branch-to-PR with human review.
- Google engineering practices: small reviewable changes.
- Martin Fowler: TDD, planning before code, branch by abstraction.
- Planning with Files: disk working memory.
