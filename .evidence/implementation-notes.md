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

## Fix History

### 2026-05-23: Codex review fixes

- **Installed-harness contract alignment.** Updated all README variants, docs (quickstart, concepts, recipes, troubleshooting), examples, templates, and SKILL.md to consistently reference `.agentic/changes`, `.agentic/runs`, `planwithfile`, `.evidence`, `AGENTS.md`, and `CLAUDE.md` as the installed harness contract. Removed all references to the earlier draft contract and installed scripts in target projects since bootstrap does not create them.
- **Forbidden-term scanner fix.** Replaced broken `cat -n` / `IFS=:` parser in `harness-lint` and `harness-check` with direct `grep -nF` scanning. The old parser split on colons but `cat -n` output uses tabs, so the scanner never actually checked file contents.
- **Removed the undocumented ignore-file feature from docs.** The ignore-file feature is not implemented. Removed documentation references in all three troubleshooting files.
- **Added MIT LICENSE file.** README references a LICENSE; now one exists.
- **Bootstrap completion message.** When target directory differs from the current directory, the completion message now correctly shows `./scripts/harness-check --target <target>` instead of a bare `./scripts/harness-check`.
- **Codex review evidence redacted.** The review file contained literal forbidden terms in its example `git grep` command; rewritten to reference "forbidden pattern set" without listing the actual terms.
