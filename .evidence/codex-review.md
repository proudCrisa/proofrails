# Codex Open-Source Readiness Review

Review target: `/Users/cdzhangxueli/Documents/workspaces/agentic-dev-harness-open-source-worktree`

Verdict: **NOT_APPROVED**

The repository is close in tone and positioning, but it is not ready to present as a high-quality beginner-friendly open-source project yet. The main blockers are documentation/script drift and broken forbidden-term scanning in the shipped validation scripts.

## Blockers

### 1. Bootstrap docs promise a different installed harness than the script creates

Severity: **High**

Evidence:

- `README.md:17-31` says the 10-minute setup creates the earlier draft installed-layout wording and installed script promises.
- `docs/quickstart.md:48-80` shows expected output for the earlier draft installed-layout wording and installed scripts.
- `scripts/bootstrap-harness:222-235` actually creates `.agentic/changes`, `.agentic/runs`, `planwithfile`, `.evidence`, `AGENTS.md`, and `CLAUDE.md` only.
- `scripts/harness-check:77-103` validates `.agentic/*`, `planwithfile`, `.evidence`, `AGENTS.md`, and `CLAUDE.md`, not the earlier draft installed-layout shape described in the docs.

Impact:

A beginner following the README or quickstart will see output and files that do not match the docs. This undermines trust in the 10-minute promise and makes troubleshooting misleading.

Recommendation:

Pick one installed-harness contract and make README, quickstart, concepts, recipes, troubleshooting, templates, bootstrap, and harness-check agree. Pick one installed-harness contract and make README, quickstart, concepts, recipes, troubleshooting, templates, bootstrap, and harness-check agree.

### 2. `harness-lint` and source-mode `harness-check` do not actually scan file contents for forbidden terms

Severity: **High**

Evidence:

- `scripts/harness-lint:84-93` reads `cat -n "$file"` output with `IFS=:` into `linenum line`. `cat -n` output has no colon separator, so `line` is empty and the forbidden-term `grep` runs against an empty string.
- `scripts/harness-check:193-202` has the same issue.
- Both commands reported success during validation, but an independent `git grep -n -F` scan was required to verify the final tracked files.

Impact:

The repository currently has no forbidden terms in tracked files, but the shipped lint/check scripts can falsely pass after a future forbidden term is introduced. That directly weakens hard check 3 and hard check 5.

Recommendation:

Use `grep -nF`/`git grep -nF` or parse `nl -ba` output correctly. Add a small self-test or fixture so a seeded forbidden term is known to fail the scanner.

## Other Issues

### 3. Troubleshooting documents features that do not exist

Severity: **Medium**

Evidence:

- `docs/troubleshooting.md:34-38` tells users to add the undocumented ignore-file feature.
- `scripts/harness-lint:71-100` has no ignore-file handling.
- `docs/quickstart.md:97` says lint scans for broken templates and structural problems, but the script only checks required files and forbidden terms.

Impact:

Beginner users will try documented remediation paths that the tool does not implement.

### 4. Open-source polish gap: README references a missing LICENSE file

Severity: **Medium**

Evidence:

- `README.md:149-151` says `MIT — see [LICENSE](./LICENSE) for details.`
- No tracked `LICENSE` file is present.

Impact:

This is a basic GitHub open-source readiness issue. It also makes the README link broken.

### 5. The bootstrap completion message is misleading for external targets

Severity: **Low**

Evidence:

- `scripts/bootstrap-harness:240-242` always says `Run ./scripts/harness-check to verify.`
- When bootstrapping a separate target, the correct command from this repository is `./scripts/harness-check --target <target>`.

Impact:

Beginners may run the source-repo check instead of validating their installed target.

## Hard Check Status

1. First screen of README says users can build AI development guardrails in 10 minutes: **PASS** (`README.md:1-5`).
2. English, Chinese, and French docs are structurally consistent: **PASS WITH NOTES**. The localized README/docs have matching section structures, but they consistently repeat the same earlier installed-layout drift against the actual scripts.
3. No forbidden terms in final tracked files: **PASS by independent scan**. `git grep -n -F` for all forbidden terms over `git ls-files` returned no matches.
4. `skill/SKILL.md` is practical for beginners and single-entry: **PASS WITH NOTES**. It is single-entry and operational, but long; the beginner path would benefit from aligning Stage 5/paths with the actual bootstrap contract.
5. Bootstrap/check/lint scripts are usable and safe: **FAIL**. They are non-destructive by default and target bootstrap works, but the forbidden-term scanner is ineffective and docs/scripts disagree.
6. gstack/OpenSpec/Superpowers framed as styles/optional integrations with fallback: **PASS** (`README.md:103-115`, `skill/SKILL.md:84-123`).
7. Validation commands:
   - `./scripts/harness-lint`: **PASS**, but scanner false-negative risk noted above.
   - `./scripts/bootstrap-harness --dry-run`: **PASS**.
   - `./scripts/harness-check`: **PASS**, but scanner false-negative risk noted above.
   - `TMP=$(mktemp -d); ./scripts/bootstrap-harness "$TMP"; ./scripts/harness-check --target "$TMP"`: **PASS**.

## Validation Notes

- Independent forbidden-term scan command used externally validated that no forbidden terms exist in tracked files.
- Result: no matches.
- Markdown fence balance check across tracked Markdown files found no odd fence counts.
- `./scripts/harness-check --target .` correctly fails because this source repository itself has not been bootstrapped as a target.

## Summary

Do not publish yet. Fix the installed-file contract drift and scanner false negatives first. After those are corrected, this should be eligible for `APPROVED_WITH_NOTES` pending a LICENSE file and minor documentation cleanup.
