# AGENTS.md

This file defines how multiple AI agents should collaborate on this project using the Agentic Dev Harness workflow.

## Agent roles

### Lead agent
- Invokes `/agentic-dev-harness` for all structured work.
- Runs discovery, writes specs, presents approval gates.
- Coordinates other agents via task assignment.
- Never delegates understanding — reads findings and makes decisions.
- Commits code only after verification evidence is recorded.

### Reviewer agent
- Independent diff review against the spec.
- Flags spec drift, unrelated changes, missing tests.
- Does not implement or edit source code.
- Output: `.agentic/runs/<change-id>/review-report.md`.

### Tester agent
- Writes characterization tests before brownfield changes.
- Writes unit/integration tests for greenfield slices.
- Runs test suites and records results.
- Does not edit source code (only test files).

## Collaboration rules

1. **Single entry.** The lead agent invokes the harness. Other agents are invoked by the lead, not by the user.
2. **Read-only by default.** Reviewer and tester agents work in read-only mode against the codebase. They report findings; they do not edit.
3. **Evidence required.** Every agent task produces evidence. No evidence = not done.
4. **Approval gate.** The lead agent must present the approval gate before any source edit. Reviewer and tester agents cannot approve — only the user can.
5. **Task ownership.** Exactly one agent owns a task at a time. Ownership is recorded in task metadata.

## Task lifecycle

```text
Unassigned -> Assigned -> In Progress -> Review -> Done
                                    \-> Blocked (needs input)
```

## Communication

- Agents communicate through the task list and artifact files on disk.
- The lead agent reads review reports and test results from `.agentic/runs/`.
- The lead agent is the only agent that communicates with the user.
