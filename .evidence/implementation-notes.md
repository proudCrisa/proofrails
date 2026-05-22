# Implementation Notes

## Architecture decisions

- **Single-entry skill.** One Markdown file with YAML frontmatter drives the entire workflow. The harness routes internally to the right sub-workflow based on project classification.
- **No mandatory dependencies.** Every optional integration (OpenSpec, gstack, Superpowers, GitNexus, gbrain) has a built-in fallback. The harness works with zero external tools beyond git and a POSIX shell.
- **Degraded-mode design.** When optional tools are missing, the harness substitutes equivalent behavior using grep, find, and manual TDD emulation. It asks once whether to install missing tools, then continues.

## Gate design

- **Approval gate.** The harness must present scope, risks, and a plan before any source-code edit. This is the single most important safety mechanism.
- **Evidence gate.** Every verification step requires build output, test results, or user-accepted exceptions. Claims without evidence are rejected.
- **Archive gate.** After archiving a change, it is terminal. Never modify archived changes in place — start a new change instead.

## File conventions

- `.agentic/changes/` holds change proposals and design documents (OpenSpec-compatible format).
- `.agentic/runs/` holds review reports, run evidence, and verification artifacts.
- `planwithfile/` is the working directory for the active change.
- `.evidence/` holds immutable evidence records. Old evidence can be archived but not deleted.

## Script design principles

- All scripts are POSIX-shell-compatible with zero dependencies beyond standard Unix tools.
- `harness-check` reports status and next steps — it never mutates.
- `harness-lint` is read-only scanning — it never mutates.
- `bootstrap-harness` creates files and directories, and only overwrites with explicit `--force`.
- Default script output is English. Translated docs (fr, zh-CN) preserve multilingual conventions.

## Cross-references

- Skill definition: `skill/SKILL.md`
- Templates: `templates/AGENTS.md`, `templates/CLAUDE.md`
- Bootstrap script: `scripts/bootstrap-harness`
- Health check: `scripts/harness-check`
- Lint scanner: `scripts/harness-lint`
