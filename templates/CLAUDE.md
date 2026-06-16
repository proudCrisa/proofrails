# CLAUDE.md

Project instructions for AI coding agents working on this repository.

## Workflow

All non-trivial changes go through the ProofRails workflow:

```text
Intake -> Probe -> Mode -> Discovery -> Spec -> Challenge -> Approve -> Apply -> Review -> Verify -> Archive
```

## Hard rules

1. **No source edits before approval.** Planning and spec documents are always allowed. Source code, dependency, and CI/CD changes require explicit user approval.
2. **Evidence over claims.** Every verification gate requires build output, test results, or explicit user-accepted exceptions.
3. **Discovery before editing.** For existing code, run read-only discovery to understand entry points, call chains, contracts, and blast radius before making changes.
4. **Spec before implementation.** Every non-trivial change gets a written proposal, design, tasks, and acceptance scenarios.
5. **Archive is terminal.** After a change is archived, start a new change rather than patching the archived one.

## Risk boundaries

Before modifying any of the following, present the change at the approval gate with explicit risk assessment:

- Public API signatures
- Database schemas and migrations
- Message queue topics and message formats
- Authentication and authorization logic
- CI/CD pipeline configuration
- Deployment configuration and infrastructure as code
- Production environment variables

## Build and test

Document your project's build and test commands here so agents can verify their work:

```bash
# Build
<your-build-command>

# Test
<your-test-command>

# Lint
<your-lint-command>

# Type check (if applicable)
<your-typecheck-command>
```

## Project-specific conventions

Add your team's conventions here. Examples:

- Code style and formatting rules
- Preferred patterns and libraries
- Naming conventions
- Error handling patterns
- Commit message format

## Required integrations

ProofRails requires this three-pack for any non-bootstrap run. Missing any one stops the workflow until installed:

- **OpenSpec** (Fission-AI) — spec lifecycle CLI. Install: `npm install -g @fission-ai/openspec@latest`. Initialize per-project with `openspec init`. ProofRails uses the `openspec/changes/<id>/` and `openspec/specs/<capability>/` layout.
- **gstack** — planning, challenge, review, QA, and ship skills. Install per https://github.com/gstack.
- **Superpowers** — TDD and execution discipline. Install the Superpowers skill pack into `~/.claude/skills/superpowers/`.

## Optional integrations

These improve quality but ProofRails works without them:

- **CodeGraph** — code graph and impact analysis via `codegraph` CLI and `codegraph_*` MCP tools. Initialize with `codegraph init -i`. Without it, ProofRails uses `grep`/`find`.
- **gbrain** — persistent memory and code search.
