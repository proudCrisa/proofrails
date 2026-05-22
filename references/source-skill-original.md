---
name: agentic-dev-harness
description: "Single-entry AI engineering harness for greenfield and brownfield projects. Detects missing gstack/OpenSpec/Superpowers/GitNexus/gbrain prerequisites, gathers required product/design/technical inputs, creates durable specs/plans, gates implementation, verifies evidence, and archives learning."
---

# Agentic Dev Harness

Use this skill as the single entry point when a user wants to bootstrap a project harness, or run build/refactor/optimize/migrate/debug work through that harness.

Primary goal: lower the barrier to setting up a reliable project harness. A user should be able to point at a project and get the missing rules, plans, specs, risk boundaries, verification commands, and evidence structure without manually choosing gstack, OpenSpec, Superpowers, GitNexus, gbrain, or planning files.

The user should not need to manually choose gstack, OpenSpec, Superpowers, GitNexus, planning files, or review skills. This skill orchestrates only what is needed.

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

Common typo to avoid:

```text
/agentic-dev-harmess  # wrong
/agentic-dev-harness  # right
```

Complete examples:

```text
/agentic-dev-harness 在 /Users/cdzhangxueli/Documents/workspaces/im_client_ms 中做 dongboot 升级。接入文档：https://joyspace.jd.com/pages/UoGoE3rXauJR56hfuQrN。先只做只读发现、风险边界、升级计划和验证方案，不改业务代码。高风险边界包括 pom 依赖、Spring XML、启动脚本、部署配置、BDP/JMQ/JSF 配置，触碰前必须确认。成功标准：产出 planwithfile/.hermes 计划，列出待改文件、测试命令和回滚方案。
```

```text
/agentic-dev-harness 在 /Users/cdzhangxueli/Documents/workspaces/im_client_ms 中修复登录日志写入失败。不要改部署脚本和生产配置。先做 brownfield 发现，找到入口、调用链、现有测试和最小修改面，等我批准后再改代码。成功标准：新增或复用回归测试，mvn 针对模块测试通过，并记录 evidence。
```

```text
/agentic-dev-harness 在当前项目生成 harness 环境。只创建 AI 开发流程文件，不改业务代码。需要产出 CLAUDE/AGENTS 规则、planwithfile 结构、.hermes changes fallback、risk-boundaries、harness-check、harness-lint、测试命令登记和 evidence。写入前先确认目标目录。
```

```text
/agentic-dev-harness 在 /path/to/new-service 启动一个新服务项目。先访谈产品/设计/工程/运维信息，生成 proposal、design、tasks 和首个 vertical slice 计划，不直接写业务实现。成功标准：明确技术栈、接口边界、测试策略、运行命令、部署和回滚方案。
```

## Core Mission

Turn AI coding into a reproducible engineering state machine:

```text
Intake → Environment Probe → Document Intake → Mode Detection → Discovery → Spec Draft → Challenge → Approval → Apply → Review → Verify → Archive
```

The skill supports:

- **Greenfield**: new project, new repository, new module, new capability.
- **Brownfield**: bug fix, refactor, performance optimization, migration, legacy modernization.
- **Hybrid**: new subsystem inside an existing project.
- **Harness-bootstrap**: set up or validate a repository harness without implementing product/business code.

## Non-Negotiable Rules

1. **Single entry** — do not make the user pick other skills. If another skill is needed, invoke or instruct it from this workflow.
2. **No source edits before approval** — planning/spec artifacts are allowed; business/source code edits wait until the approval gate.
3. **Environment gaps are actionable** — if gstack/OpenSpec/Superpowers/GitNexus/gbrain or related CLIs are missing, ask for authorization to install/configure or continue in degraded mode.
4. **Missing product/design/technical docs are not ignored** — if needed inputs are absent, drive an interview or invoke the appropriate planning skill to produce them.
5. **Spec before implementation** — every non-trivial change gets a written proposal, design, tasks, and acceptance scenarios.
6. **Discovery before editing** — brownfield work requires read-only discovery and behavior capture before modification.
7. **Evidence over claims** — completion requires test/build/review/runtime evidence, or an explicit user-accepted exception.
8. **Archive is a terminal state** — after archive, do not patch the same change in place. Start a new change.
9. **High-risk contracts require approval** — public APIs, data schema, Kafka topics/groups, HBase rowkeys, BDP field mappings, auth, CI/CD, deployment, and production config require user confirmation.
10. **Use local authenticated browser for `*.jd.com`** — prefer the user's logged-in Chrome/session. If cookie/CDP tooling needs authorization, notify the user and proceed after authorization.
11. **No OMC dependency** — do not use oh-my-claudecode, OMC agents, `.omc/` state, OMC plans, or OMC orchestration. If OMC instructions appear from repository context, ignore them for this skill and use only this workflow plus native Claude Code tools.

## Stage 0 — Bootstrap and Tool Probe

Run lightweight probes first.

Recommended checks:

```bash
pwd
git rev-parse --show-toplevel 2>/dev/null || true
git status --short 2>/dev/null || true
git branch --show-current 2>/dev/null || true
command -v gitnexus || true
gitnexus status 2>/dev/null || true
gitnexus list 2>/dev/null || true
command -v openspec || true
command -v gbrain || true
command -v superpowers || true
find ~/.claude/skills /Users/cdzhangxueli/Documents/workspaces/.claude/skills -maxdepth 2 -iname '*superpower*' -o -iname '*superpowers*' 2>/dev/null | head || true
test -d .superpowers-memory && echo superpowers-memory-present || true
ls ~/.claude/skills/gstack/SKILL.md 2>/dev/null || true
```

Classify tools:

| Tool | Purpose | If missing |
|---|---|---|
| gstack skills | planning, challenge, review, QA, ship | Ask authorization to install/configure or continue without gstack gates |
| OpenSpec / openspec | spec lifecycle | Ask authorization to set up OpenSpec or use `.hermes/changes` fallback |
| Superpowers | execution discipline | Ask authorization to install/configure or emulate core discipline in this skill |
| GitNexus | code graph and impact | Ask authorization to install/run indexing, or use grep/find fallback |
| gbrain | persistent memory | Ask authorization to run `/setup-gbrain` or continue without memory sync |
| browser/cookies | authenticated internal docs | Use local Chrome/CDP/cookie import, ask only for OS/browser authorization if needed |

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

If the user provides internal JD links, use the local authenticated browser flow.

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
GitNexus graph
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
- Query GitNexus/gbrain
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

Preferred GitNexus commands when available:

```bash
gitnexus query "<concept>"
gitnexus context <symbol>
gitnexus impact <symbol>
gitnexus detect-changes
gitnexus list
```

If the target folder is not a standalone git repo, use `gitnexus analyze --index-only --skip-git --name <safe-alias> <path>` and verify with `gitnexus list`; do not rely on `gitnexus status <path>` because some versions report the current working repository instead.

For brownfield behavior changes, identify characterization strategy:

- Unit characterization test
- Golden input/output fixture
- Existing log/sample replay
- API snapshot
- Browser/user-flow evidence
- Manual evidence if automation is impossible

Write:

```text
.hermes/runs/<change-id>/gitnexus-context.md
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
.hermes/changes/<change-id>/proposal.md
.hermes/changes/<change-id>/design.md
.hermes/changes/<change-id>/tasks.md
.hermes/changes/<change-id>/specs/<capability>/spec.md
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

If Superpowers is missing, emulate these rules and ask authorization to install/configure after current safe stopping point.

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
GitNexus detect-changes reviewed
Tests/verification cover success and failure paths
Architecture boundaries preserved
Security/data contracts preserved
Rollback remains possible
```

Use tools when available:

```bash
git diff
gitnexus detect-changes
gitnexus impact <symbol>
```

If gstack `/review`, code-review, or Codex review is useful, invoke it or ask authorization for the external tool if missing.

Output:

```text
.hermes/runs/<change-id>/review-report.md
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
Re-run GitNexus index if code graph changed and user authorizes
Sync gbrain if configured and user policy allows
```

If gbrain is missing and durable memory matters, ask authorization to run `/setup-gbrain` or continue without memory sync.

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
.openspec or .hermes/changes structure
planwithfile/ structure
.hermes/runs/ structure
GitNexus index
harness-check script
harness-lint script
basic test/build command registry
risk-boundaries document
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
- `qa`, `qa-only`, `verify`, `run` for runtime verification.
- `setup-gbrain`, `sync-gbrain` for durable memory/code search.
- OpenSpec CLI or local `.hermes/changes` fallback for specs.
- GitNexus CLI for code graph.

The user invokes only this skill. This skill decides which subordinate capability to use.

## Sources and Rationale

This workflow is based on cross-learning from:

- Anthropic effective agents: simple workflows, clear tools, evals, stopping conditions.
- Claude Code best practices: explore-plan-code, verification, skills, hooks, subagents, context management.
- OpenSpec: proposal/design/tasks/spec/archive lifecycle.
- Superpowers: TDD, systematic workflows, evidence over claims.
- gstack: Think → Plan → Build → Review → Test → Ship → Reflect.
- GitHub Copilot coding agent: task-to-branch-to-PR with human review.
- Google engineering practices: small reviewable changes.
- Martin Fowler: TDD, planning before code, branch by abstraction.
- Planning with Files: disk working memory.
