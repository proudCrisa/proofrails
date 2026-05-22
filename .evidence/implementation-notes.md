# Implementation Notes

## Architecture decisions

- **Single-entry skill.** One Markdown file with YAML frontmatter drives the entire workflow. ProofRails routes internally to the right sub-workflow based on project classification.
- **No mandatory dependencies.** Every optional integration (OpenSpec, gstack, Superpowers, GitNexus, gbrain) has a built-in fallback. ProofRails works with zero external tools beyond git and a POSIX shell.
- **Degraded-mode design.** When optional tools are missing, ProofRails substitutes equivalent behavior using grep, find, and manual TDD emulation. It asks once whether to install missing tools, then continues.

## Gate design

- **Approval gate.** The harness must present scope, risks, and a plan before any source-code edit. This is the single most important safety mechanism.
- **Evidence gate.** Every verification step requires build output, test results, or user-accepted exceptions. Claims without evidence are rejected.
- **Archive gate.** After archiving a change, it is terminal. Never modify archived changes in place — start a new change instead.

## File conventions

- `.agentic/changes/` holds change proposals and design documents (OpenSpec-compatible format).
- `.agentic/runs/` holds review reports, run evidence, and verification artifacts.
- `planwithfile/` is the working directory for the active change.
- `.evidence/` holds verification evidence for the open-source package.

## Script design principles

- All scripts are POSIX-shell-compatible with zero dependencies beyond standard Unix tools.
- `proofrails-check` reports status and next steps — it never mutates.
- `proofrails-lint` is read-only scanning — it never mutates.
- `proofrails-bootstrap` creates files and directories, and only overwrites with explicit `--force`.
- Default script output is English. Translated docs (fr, zh-CN) preserve multilingual conventions.

## Cross-references

- Skill definition: `skill/SKILL.md`
- Templates: `templates/AGENTS.md`, `templates/CLAUDE.md`
- Bootstrap script: `scripts/proofrails-bootstrap`
- Health check: `scripts/proofrails-check`
- Lint scanner: `scripts/proofrails-lint`

## Verification history

### 2026-05-23: Open-source package verification

- **Installed contract alignment.** README variants, docs, examples, templates, and SKILL.md consistently reference `.agentic/changes`, `.agentic/runs`, `planwithfile`, `.evidence`, `AGENTS.md`, and `CLAUDE.md` as the installed ProofRails contract.
- **Forbidden-term scanner.** `proofrails-lint` and `proofrails-check` use direct `grep -nF` scanning against the configured forbidden pattern set.
- **Documented features only.** Troubleshooting docs avoid undocumented ignore-file behavior.
- **License.** The package includes an MIT LICENSE file.
- **Bootstrap completion message.** When target directory differs from the current directory, the completion message shows `./scripts/proofrails-check --target <target>`.
