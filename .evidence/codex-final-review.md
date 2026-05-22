# Codex Final Re-Review

Verdict: NOT_APPROVED

## Scope

Independent final re-review of the open-source Agentic Dev Harness repository, focused on the requested hard checks and the prior blocker around installed-project users running bare `harness-check`.

## Findings

### Blocker: installed contract is still contradicted in troubleshooting docs

The installed-target contract requires:

- `.agentic/changes`
- `.agentic/runs`
- `planwithfile`
- `.evidence`
- `AGENTS.md`
- `CLAUDE.md`

`scripts/harness-check` enforces that contract in target mode:

- `scripts/harness-check:65-72` auto-detects a target only when `.agentic/changes`, `.agentic/runs`, `planwithfile`, `AGENTS.md`, and `CLAUDE.md` exist.
- `scripts/harness-check:100-118` validates `.agentic/changes`, `.agentic/runs`, `planwithfile`, `.evidence`, `CLAUDE.md`, and `AGENTS.md`.

However, the troubleshooting docs still tell OpenSpec users they may remove the whole installed harness state directory:

- `docs/troubleshooting.md:65-73`
- `docs/troubleshooting.zh-CN.md:65-73`
- `docs/troubleshooting.fr.md:65-73`

That conflicts with the installed validator and with the installed contract named in this review. If a user follows that troubleshooting guidance, bare `harness-check` will not detect target mode and `harness-check --target <path>` will fail because `.agentic/runs` is missing.

## Checks

1. README first screen: PASS. `README.md:1-27` includes the 10-minute AI development guardrails promise and the installed layout.
2. English, Chinese, French doc structure: PASS. The README and `docs/{quickstart,concepts,recipes,troubleshooting}` files have matching section structure across EN/ZH/FR.
3. Forbidden-term scan: PASS. `./scripts/harness-lint`, `./scripts/harness-check`, and an additional encoded scan across `git ls-files` found no matches. This report intentionally does not spell out the scanned terms.
4. Installed contract agreement: FAIL. README, quickstart, concepts, templates, skill, and scripts mostly agree on the required installed layout, but troubleshooting contradicts it by allowing removal of `.agentic/` entirely.
5. File-content scanning: PASS. `scripts/harness-lint` and `scripts/harness-check` both enumerate candidate files and use fixed-string file-content scans with line reporting.
6. Required commands: PASS.
   - `./scripts/harness-lint`
   - `./scripts/bootstrap-harness --dry-run`
   - `./scripts/harness-check`
   - Installed-target sequence with `mktemp`, dry-run non-creation assertion, real bootstrap, bare check from inside the target, and explicit `--target` check.
7. LICENSE: PASS. `LICENSE` exists and contains the MIT license.
8. Skill beginner-friendliness and single entry: PASS. `skill/SKILL.md` presents `/agentic-dev-harness` as the primary single-entry workflow, includes invocation examples, explains degraded-mode behavior, and routes optional tools internally.

## Command Evidence

All requested commands exited 0:

```bash
./scripts/harness-lint
./scripts/bootstrap-harness --dry-run
./scripts/harness-check
TMP=$(mktemp -d); ./scripts/bootstrap-harness --dry-run "$TMP"; test ! -e "$TMP/.agentic"; ./scripts/bootstrap-harness "$TMP"; (cd "$TMP" && /Users/cdzhangxueli/Documents/workspaces/agentic-dev-harness-open-source-worktree/scripts/harness-check); ./scripts/harness-check --target "$TMP"
```

The installed-target sequence confirmed the previous blocker is fixed for a freshly bootstrapped installed project: bare `harness-check` from inside the target ran in target mode and passed.

## Required Fix

Update the three troubleshooting files so they do not tell users to remove the whole installed harness state directory. They should explain the OpenSpec fallback while preserving the installed harness evidence directory required for validation.
