# ProofRails

**Build AI development guardrails in 10 minutes.**

A single-entry skill that turns AI-assisted coding into a reproducible engineering workflow. Point it at any project — greenfield or brownfield — and get specs, plans, risk boundaries, verification gates, and evidence tracking without manually wiring together a dozen tools.

[English](./README.md) | [中文](./README.zh-CN.md) | [Français](./README.fr.md)

---

## Why this exists

AI coding agents are powerful but unpredictable. They skip discovery, edit without approval, merge without tests, and leave no evidence behind. Teams end up writing ad-hoc rules, copying CLAUDE.md templates, and manually enforcing gates — work that has nothing to do with their actual product.

ProofRails gives you that infrastructure in one command. It is not a framework, not a SaaS, not a proprietary platform. It is a **skill** — a structured workflow that runs inside your existing AI coding tool — plus a set of scripts and templates that keep your project in a known-good state.

## What you get in 10 minutes

```text
your-project/
├── CLAUDE.md                    # AI agent project instructions
├── AGENTS.md                    # Multi-agent collaboration rules
├── .proofrails/
│   ├── changes/                 # Change proposals and design docs
│   └── runs/                    # Review reports and evidence
├── planwithfile/                # Working directory for active changes
└── .evidence/                   # Immutable evidence records
```

## How it works

```
Intake -> Probe -> Mode -> Discovery -> Spec -> Challenge -> Approve -> Apply -> Review -> Verify -> Archive
```

1. **Intake** — Collect goal, constraints, success criteria. Ask only what is missing.
2. **Probe** — Detect available tools (git, OpenSpec, gstack, etc.). Map degraded-mode fallbacks.
3. **Mode** — Classify the work: greenfield, brownfield, hybrid, or proofrails-bootstrap.
4. **Discovery** — Read-only exploration. Find entry points, contracts, risks. No edits yet.
5. **Spec** — Write proposal, design, tasks, and acceptance scenarios (OpenSpec-compatible).
6. **Challenge** — Stress-test the spec from business, risk, implementation, and history angles.
7. **Approve** — Present scope, risks, and plan. Require explicit approval before any source edit.
8. **Apply** — Implement in small, reviewable slices with tests first.
9. **Review** — Diff against spec. Flag drift. Require resolution.
10. **Verify** — Build, test, lint, smoke. Evidence or it did not happen.
11. **Archive** — Update durable docs. Record lessons. Terminal state for this change.

## Quickstart

### Prerequisites

- git (any recent version)
- A POSIX-compatible shell (bash, zsh, dash)
- An AI coding tool that supports skills (Claude Code, or any tool with Markdown-based skill loading)

### Install

```bash
# Clone into your project or skills directory
git clone https://github.com/proofrails/proofrails.git

# Claude Code
mkdir -p ~/.claude/skills/proofrails
cp proofrails/skill/SKILL.md ~/.claude/skills/proofrails/SKILL.md

# OpenClaw / Hermes-style Markdown skills
mkdir -p ~/.openclaw/workspace/skills/proofrails
cp proofrails/skill/SKILL.md ~/.openclaw/workspace/skills/proofrails/SKILL.md

# Other Markdown-skill agents: copy skill/SKILL.md to that tool's skill directory.
```

### Bootstrap a project

```bash
# In your project root:
./scripts/proofrails-bootstrap

# Or dry-run first to see what will be created:
./scripts/proofrails-bootstrap --dry-run

# Check ProofRails health:
./scripts/proofrails-check

# Lint for issues:
./scripts/proofrails-lint
```

Use the ProofRails scripts as the canonical interface: `proofrails-bootstrap`, `proofrails-check`, and `proofrails-lint`.

### Use the skill

```
/proofrails in ./my-project fix the payment timeout bug.
Start with brownfield discovery. Wait for approval before editing code.
Success criteria: regression test passes, module tests green.
```

Use `/proofrails` as the canonical skill invocation.

## Modes

| Mode | When to use | Key constraint |
|---|---|---|
| **Greenfield** | New project, new repo, new module | Require product/design/tech docs or interview |
| **Brownfield** | Bug fix, refactor, migration, perf | Discovery before edits, characterization tests |
| **Hybrid** | New subsystem inside existing project | Combine greenfield spec + brownfield discovery |
| **ProofRails-bootstrap** | Set up ProofRails itself | No business code touched |

## Compatibility

ProofRails works with any AI coding tool that can read Markdown skill files. It has optional integrations with:

| Integration | Purpose | Without it |
|---|---|---|
| [OpenSpec](https://github.com/anthropics/openspec) | Spec lifecycle CLI | Falls back to `.proofrails/changes/` |
| [gstack](https://github.com/gstack) | Planning/review/QA skills | Built-in workflow gates work standalone |
| Superpowers | TDD and execution discipline | Core rules are emulated in ProofRails |
| GitNexus | Code graph and impact analysis | grep/find fallback |
| gbrain | Persistent memory and code search | Works without it |

**ProofRails has zero hard dependencies.** Install what helps; skip what does not.

## Safety principles

1. **No edits before approval.** Discovery and spec writing are always allowed. Source changes, dependency changes, and CI/CD changes require explicit user approval.
2. **Evidence over claims.** Every verification gate requires build output, test results, or explicit user-accepted exceptions.
3. **High-risk contracts require confirmation.** Public APIs, data schemas, message queues, authentication, deployment, and production configuration require user approval before modification.
4. **Archive is terminal.** After a change is archived, do not patch it in place. Start a new change.
5. **Single entry.** The user invokes one skill. ProofRails routes to sub-skills internally. The user never needs to manually choose between planning, review, QA, or ship skills.

## Examples

- [Minimal ProofRails setup](./examples/minimal/README.md) — Bootstrap on an empty repo
- [Brownfield bug fix](./examples/brownfield/README.md) — Discovery, spec, fix, verify on an existing project
- [Greenfield service](./examples/greenfield/README.md) — New service from scratch with full ProofRails

## Documentation

| Document | EN | ZH | FR |
|---|---|---|---|
| Quickstart | [en](./docs/quickstart.md) | [zh](./docs/quickstart.zh-CN.md) | [fr](./docs/quickstart.fr.md) |
| Concepts | [en](./docs/concepts.md) | [zh](./docs/concepts.zh-CN.md) | [fr](./docs/concepts.fr.md) |
| Recipes | [en](./docs/recipes.md) | [zh](./docs/recipes.zh-CN.md) | [fr](./docs/recipes.fr.md) |
| Troubleshooting | [en](./docs/troubleshooting.md) | [zh](./docs/troubleshooting.zh-CN.md) | [fr](./docs/troubleshooting.fr.md) |

## Contributing

Contributions are welcome. Before submitting a PR:

1. Run `./scripts/proofrails-lint` and fix all issues.
2. Run `./scripts/proofrails-check` and verify all checks pass.
3. Add evidence to `.evidence/` for any new behavior.
4. Update docs if you change the workflow.

## License

MIT — see [LICENSE](./LICENSE) for details.

---

**Built for teams that want AI speed with engineering rigor.**
