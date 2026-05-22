# Codex Re-Review

Review target: `/Users/cdzhangxueli/Documents/workspaces/agentic-dev-harness-open-source-worktree`

Verdict: **NOT_APPROVED**

The original contract and scanner blockers are mostly fixed: the installed file set now centers on `.agentic/changes`, `.agentic/runs`, `planwithfile`, `.evidence`, `AGENTS.md`, and `CLAUDE.md`; the scanner now checks real file contents; required commands pass; and `LICENSE` exists. One beginner-facing docs/scripts drift remains in the quickstart validation flow.

## Blocker

### Quickstart tells beginners to run the wrong validation mode

Severity: **High**

Evidence:

- `docs/quickstart.md:36-78`, `docs/quickstart.zh-CN.md:36-78`, and `docs/quickstart.fr.md:36-78` tell users to bootstrap a copied project and then run bare `./scripts/harness-check`, with expected output for an installed target.
- `scripts/harness-check:34-37` defaults to `MODE="source"` unless `--target <path>` is passed.
- `scripts/harness-check:124-164` source mode checks source-repository files and executable scripts, not only the installed target contract.
- Following the documented copied-project flow in a temporary directory produced exit status `1`; bare `./scripts/harness-check` entered source mode and reported missing README files instead of validating the installed harness.

Impact:

A beginner can follow the quickstart, successfully run bootstrap, and then get a failing health check even though the installed harness files exist. This keeps the previous "misleading bootstrap/check docs" issue partially unresolved.

Required fix:

Pick one behavior and make docs/scripts agree:

- If bare `./scripts/harness-check` is meant to validate an installed project, change the script default or auto-detect installed-target mode.
- If source mode is the intended default, update README and all quickstart variants to show `./scripts/harness-check --target .` for installed-project validation, and update expected output accordingly.

## Hard Check Status

1. README first screen has the 10-minute AI development guardrails promise: **PASS** (`README.md:1-5`).
2. English, Chinese, and French docs are structurally consistent: **PASS**. The README and docs heading structures match across the three languages.
3. No forbidden pattern set matches in final tracked files: **PASS**. Independent tracked-file scan returned `0` matches.
4. README/docs/templates/skill/scripts agree on the installed file contract: **PASS WITH NOTE**. The file set is aligned, but the quickstart validation command still disagrees with `harness-check` mode behavior.
5. `harness-lint` and `harness-check` scan file contents: **PASS**. Both scripts now use `grep -nF` against file content. A temp-copy negative test seeded one member of the forbidden pattern set and both scripts exited `1` with one detection.
6. Required scripts pass: **PASS**.
   - `./scripts/harness-lint`: exit `0`.
   - `./scripts/bootstrap-harness --dry-run`: exit `0`.
   - `./scripts/harness-check`: exit `0`.
   - `TMP=$(mktemp -d); ./scripts/bootstrap-harness --dry-run "$TMP"; test ! -e "$TMP/.agentic"; ./scripts/bootstrap-harness "$TMP"; ./scripts/harness-check --target "$TMP"`: exit `0`.
7. `LICENSE` exists: **PASS**.
8. Skill is beginner-friendly and single-entry: **FAIL** until the quickstart validation path is corrected. The skill itself is single-entry, but the beginner setup path still leads to a false failure.

## Notes

- The previous missing `LICENSE` issue is resolved.
- The previous scanner false-pass issue is resolved.
- The old installed-contract naming drift is resolved for the created target files.
- The worktree has staged, unstaged, and untracked changes unrelated to this report; this review evaluates the current filesystem and tracked-file scan as requested.
