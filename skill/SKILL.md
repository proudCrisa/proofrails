---
name: agentic-dev-harness
description: "Single-entry AI engineering harness for greenfield and brownfield projects. Detects missing tool prerequisites, gathers required product/design/technical inputs, creates durable specs/plans, gates implementation, verifies evidence, and archives learning."
version: "1.0.0"
author: "Agentic Dev Harness Contributors"
license: "MIT"
metadata:
  hermes:
    tags:
      - harness
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
---

# Agentic Dev Harness

Use this skill as the single entry point when a user wants to bootstrap a project harness, or run build/refactor/optimize/migrate/debug work through that harness.

Primary goal: lower the barrier to setting up a reliable project harness. A user should be able to point at a project and get the missing rules, plans, specs, risk boundaries, verification commands, and evidence structure without manually choosing planning tools, spec formats, or review skills.

The user should not need to manually choose gstack, OpenSpec, Superpowers, planning files, or review skills. This skill orchestrates only what is needed.

## Invocation Contract

Invoke this skill with one complete task sentence. Include the target project path, goal, constraints, success criteria, and any docs/links when available.

Canonical command:

```text
/agentic-dev-harness <target project> <goal> <constraints> <success criteria> <docs or links>
```

Compatibility alias:

```text
/agentic-dv-harness <same arguments>
```

Complete examples:

```text
/agentic-dev-harness in /home/user/projects/payment-service upgrade the auth framework. Docs: https://wiki.example.com/auth-migration. Start with read-only discovery, risk boundaries, upgrade plan, and verification approach. Do not modify business code yet. High-risk boundaries include dependency manifests, auth configuration, startup scripts, and deployment config. Success criteria: produce a planwithfile/ plan listing files to change, test commands, and rollback steps.
```

```text
/agentic-dev-harness in /home/user/projects/web-app fix login audit log write failures. Do not touch deployment scripts or production config. Start with brownfield discovery: find entry points, call chain, existing tests, and minimal change surface. Wait for approval before editing code. Success criteria: new or reused regression tests, module tests pass, and evidence recorded.
```

```text
/agentic-dev-harness in the current project generate a harness environment. Create AI development workflow files only, no business code changes. Produce CLAUDE/AGENTS rules, planwithfile structure, .agentic changes fallback, harness environment files. Confirm target directory before writing.
```

```text
/agentic-dev-harness in /path/to/new-service start a new service project. Interview for product/design/engineering/operations inputs first. Generate proposal, design, tasks, and first vertical slice plan. Do not write business implementation yet. Success criteria: clear tech stack, interface boundaries, test strategy, run commands, deploy and rollback plan.
```

## Core Mission

Turn AI coding into a reproducible engineering state machine:

```text
Intake -> Environment Probe -> Document Intake -> Mode Detection -> Discovery -> Spec Draft -> Challenge -> Approval -> Apply -> Review -> Verify -> Archive
```

The skill supports:

- **Greenfield**: new project, new repository, new module, new capability.
- **Brownfield**: bug fix, refactor, performance optimization, migration, legacy modernization.
- **Hybrid**: new subsystem inside an existing project.
- **Harness-bootstrap**: set up or validate a repository harness without implementing product/business code.

## Non-Negotiable Rules

1. **Single entry** — do not make the user pick other skills. If another skill is needed, invoke or instruct it from this workflow.
2. **No source edits before approval** — planning/spec artifacts are allowed; business/source code edits wait until the approval gate.
3. **Environment gaps are actionable** — if optional tools (gstack/OpenSpec/Superpowers/GitNexus/gbrain) are missing, offer to continue in degraded mode with built-in fallbacks.
4. **Missing product/design/technical docs are not ignored** — if needed inputs are absent, drive an interview or invoke the appropriate planning skill to produce them.
5. **Spec before implementation** — every non-trivial change gets a written proposal, design, tasks, and acceptance scenarios.
6. **Discovery before editing** — brownfield work requires read-only discovery and behavior capture before modification.
7. **Evidence over claims** — completion requires test/build/review/runtime evidence, or an explicit user-accepted exception.
8. **Archive is a terminal state** — after archive, do not patch the same change in place. Start a new change.
9. **High-risk contracts require approval** — public APIs, data schema, message queues, auth, CI/CD, deployment, and production config require user confirmation.
10. **Use local authenticated browser for internal docs** — prefer the user's logged-in session for internal wiki/docs. Ask for authorization if cookie/browser tooling is needed.

## Stage 0 — Bootstrap and Tool Probe

Run lightweight probes first.

Recommended checks:

```bash
pwd
git rev-parse --show-toplevel 2>/dev/null || true
git status --short 2>/dev/null || true
git branch --show-current 2>/dev/null || true
command -v openspec || true
command -v gbrain || true
command -v gitnexus || true
test -d .superpowers-memory && echo superpowers-memory-present || true
ls ~/.claude/skills/gstack/SKILL.md 2>/dev/null || true
```

Classify tools:

| Tool | Purpose | If missing |
|---|---|---|
| gstack skills | planning, challenge, review, QA, ship | Continue with built-in workflow gates |
| OpenSpec CLI | spec lifecycle | Use `.agentic/changes` fallback directory |
| Superpowers | execution discipline | Emulate core discipline in this skill |
| GitNexus | code graph and impact | Use grep/find fallback |
| gbrain | persistent memory | Continue without memory sync |
| browser/cookies | authenticated internal docs | Use local browser session or skip |

### Environment Authorization Rule

If a tool is absent and required for the requested outcome, ask:

```text
I need <tool> for <reason>. May I install/configure it now?
```

Offer choices:

1. Install/configure now.
2. Continue in degraded mode.
3. Stop and let user set it up manually.

Do not silently skip a missing tool if it changes workflow quality.

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
harness-bootstrap
```

Rules:

- Empty/new repo or new product: greenfield.
- Existing production system change: brownfield.
- New capability inside existing system: hybrid.
- Request is to create, validate, or repair the AI development workflow itself: harness-bootstrap.

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
Code graph / GitNexus
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
What calls it and what does it call?
What config/data contracts does it depend on?
What tests or fixtures exist?
What hidden compatibility risks exist?
What is the blast radius of changing it?
```

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

## Stage 5 — Spec Draft

Create OpenSpec-style artifacts.

If `.openspec` exists, use:

```text
.openspec/changes/<change-id>/proposal.md
.openspec/changes/<change-id>/design.md
.openspec/changes/<change-id>/tasks.md
.openspec/changes/<change-id>/specs/<capability>/spec.md
```

If `.openspec` does not exist, use fallback:

```text
.agentic/changes/<change-id>/proposal.md
.agentic/changes/<change-id>/design.md
.agentic/changes/<change-id>/tasks.md
.agentic/changes/<change-id>/specs/<capability>/spec.md
```

Use this fallback by default for low-risk `harness-bootstrap` validation runs. Only create a full `.openspec` tree after explicit user approval.

If the user's goal is to bootstrap a full harness environment, propose creating `.openspec` as part of the setup.

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
Artifacts written
High-risk boundaries
Implementation slices
Verification plan
Rollback plan
Open decisions
```

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
Harness scripts
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

For each task:

```text
Read task
Confirm exact files
Write/update tests first when practical
Make minimal change
Run targeted check
Record evidence
Mark task complete only after evidence
```

If Superpowers is installed, follow its relevant workflow principles:

- TDD when practical.
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
.agentic/runs/<change-id>/review-report.md
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

## Harness Bootstrap Mode

If the user asks to set up a repository harness, this skill should create or propose:

```text
CLAUDE.md / AGENTS.md project rules
.openspec or .agentic/changes structure
planwithfile/ structure
.agentic/runs/ structure
.evidence/ structure
harness-check script
harness-lint script
basic test/build command registry
skill routing notes
```

For a new project, require product/design/technical inputs first. If absent, interview the user or invoke planning skills to produce them.

For an existing project, run read-only discovery first and infer current build/test commands before writing harness scripts.

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
DONE — harness stage completed with evidence.
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
- OpenSpec CLI or local `.agentic/changes` fallback for specs.
- GitNexus CLI for code graph.

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
