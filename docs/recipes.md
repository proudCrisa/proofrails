# Recipes

Common workflows and how to execute them with the harness.

## Fix a bug (Brownfield)

### 1. Invoke the harness

```
/agentic-dev-harness in ./my-project fix orders with zero quantity crashing the inventory sync.
Start with brownfield discovery. Do not touch deployment config.
Success criteria: regression test passes, order module tests green.
```

### 2. What the harness does

1. Probes environment (git, available tools).
2. Classifies as brownfield.
3. Runs read-only discovery:
   - Finds the order processing entry point.
   - Traces the call chain to inventory sync.
   - Finds existing tests in `order/` module.
   - Identifies risk boundaries (order schema, inventory queue).
4. Writes a spec: proposal, design, tasks, acceptance scenarios.
5. Challenges the spec (what if quantity is negative? what about partial refunds?).
6. Presents the plan and asks for approval.
7. After approval: writes a characterization test that reproduces the zero-quantity crash.
8. Implements the fix (guard clause for zero quantity).
9. Runs the test suite, verifies the fix.
10. Reviews diff against spec.
11. Records evidence.
12. Archives the change.

### 3. What you do

- Answer discovery questions the harness cannot answer from code alone.
- Approve or revise the spec at the approval gate.
- Provide any environment-specific context (staging DB, test credentials).

## Start a new service (Greenfield)

### 1. Invoke the harness

```
/agentic-dev-harness in /path/to/new-service start a user notification service.
No existing code. Tech stack preference: TypeScript, PostgreSQL, Redis.
Success criteria: proposal, design, tasks, first vertical slice plan.
Do not write implementation yet.
```

### 2. What the harness does

1. Probes environment.
2. Classifies as greenfield.
3. Drives a structured interview (product, design, engineering, operations).
4. Writes proposal.md, design.md, tasks.md.
5. Identifies the smallest vertical slice (e.g., "send email on user signup").
6. Challenges the design.
7. Presents the plan and asks for approval.
8. After approval: generates project skeleton, first vertical slice tests.
9. Implements the slice.
10. Reviews, verifies, archives.

### 3. What you do

- Answer the interview questions.
- Provide product requirements if available.
- Approve architecture decisions at the challenge gate.

## Refactor a module (Brownfield)

### 1. Invoke the harness

```
/agentic-dev-harness in ./my-project refactor the payment module to use the new gateway API.
Keep the existing public interface. All existing tests must pass.
Success criteria: same test pass rate, no API contract changes, performance within 5%.
```

### 2. Key harness behaviors

- Discovery identifies all callers of the payment module.
- Characterization tests capture current behavior before refactoring.
- Spec defines the seam/adapter strategy.
- Implementation uses branch-by-abstraction.
- Verify runs the full test suite and compares performance.

## Bootstrap a harness on an existing project

### 1. Invoke the harness

```
/agentic-dev-harness in ./my-project set up the AI development harness.
Do not modify business code. Only create harness files.
Success criteria: harness-check passes, harness-lint clean.
```

### 2. What the harness does

1. Probes the project: language, build system, test commands, existing CLAUDE.md.
2. Classifies as harness-bootstrap.
3. Discovers existing build/test/lint commands from package.json, Makefile, etc.
4. Creates or updates CLAUDE.md with project-specific instructions.
5. Creates AGENTS.md for multi-agent rules.
6. Creates .agentic/ and planwithfile/ directories.
7. Runs harness-check and harness-lint to validate.
8. Records evidence.

### 3. What you do

- Confirm the harness's understanding of your build/test commands.
- Review and customize CLAUDE.md for your team's conventions.

## Run the harness in degraded mode

When optional tools (gstack, OpenSpec CLI, Superpowers, GitNexus, gbrain) are not installed, the harness uses built-in fallbacks:

| Missing tool | Degraded behavior |
|---|---|
| gstack | Skip gstack-specific challenge skills. Use built-in four-perspective challenge. |
| OpenSpec CLI | Write specs to `.agentic/changes/` instead of `.openspec/`. Same format. |
| Superpowers | Emulate TDD, systematic debug, small tasks manually. |
| GitNexus | Use `grep -r` and `find` for code search. |
| gbrain | Skip memory sync. Artifacts on disk are the memory. |

The harness always works in degraded mode. It asks once whether you want to install missing tools, then proceeds with whatever is available.

## Customize templates

All templates in `templates/` are starting points. Customize them for your team:

### CLAUDE.md

Add your team's conventions, preferred stack, and project-specific rules:

```markdown
# Additional project rules

## Stack
- Backend: Go 1.22, chi router, pgx
- Frontend: React 18, TypeScript, Vite
- Database: PostgreSQL 16

## Conventions
- Errors wrap with fmt.Errorf("%w", err)
- HTTP handlers return error, never write status directly
- Tests use testify/assert
```

### AGENTS.md

Define how multiple AI agents should collaborate:

```markdown
# Agent roles
- lead: coordinates, runs harness, seeks approval
- reviewer: independent diff review, no implementation
- tester: writes and runs tests, never edits source
```
