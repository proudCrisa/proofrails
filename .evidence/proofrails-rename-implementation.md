# ProofRails Rename Implementation Evidence

## Summary

Renamed the public project brand from Agentic Dev Harness to ProofRails across the open-source docs, skill metadata, templates, examples, scripts, and license.

## Changed files

- `README.md`
- `README.zh-CN.md`
- `README.fr.md`
- `docs/quickstart.md`
- `docs/quickstart.zh-CN.md`
- `docs/quickstart.fr.md`
- `docs/concepts.md`
- `docs/concepts.zh-CN.md`
- `docs/concepts.fr.md`
- `docs/recipes.md`
- `docs/recipes.zh-CN.md`
- `docs/recipes.fr.md`
- `docs/troubleshooting.md`
- `docs/troubleshooting.zh-CN.md`
- `docs/troubleshooting.fr.md`
- `examples/minimal/README.md`
- `examples/brownfield/README.md`
- `examples/greenfield/README.md`
- `templates/AGENTS.md`
- `templates/CLAUDE.md`
- `skill/SKILL.md`
- `LICENSE`
- `scripts/proofrails-bootstrap`
- `scripts/proofrails-check`
- `scripts/proofrails-lint`
- `scripts/bootstrap-harness`
- `scripts/harness-check`
- `scripts/harness-lint`

## Implementation notes

- Skill metadata now uses `name: proofrails`.
- Canonical invocation is `/proofrails`.
- Legacy invocations `/agentic-dev-harness` and `/agentic-dv-harness` are preserved as compatibility aliases.
- Primary scripts are now `proofrails-bootstrap`, `proofrails-check`, and `proofrails-lint`.
- Legacy scripts `bootstrap-harness`, `harness-check`, and `harness-lint` remain executable compatibility wrappers that delegate to the new scripts.
- English, Chinese, and French docs now use the ProofRails brand and primary script names.
- Headline tagline remains exactly `Build AI development guardrails in 10 minutes.` in `README.md`, with equivalent Chinese and French translations.
- No new internal company/platform content was added.

## Commands run

```bash
./scripts/proofrails-lint
./scripts/proofrails-check
./scripts/harness-lint
./scripts/harness-check
TMP=$(mktemp -d); ./scripts/proofrails-bootstrap --dry-run "$TMP"; test ! -e "$TMP/.agentic"; ./scripts/proofrails-bootstrap "$TMP"; ./scripts/proofrails-check --target "$TMP"; (cd "$TMP" && /Users/cdzhangxueli/Documents/workspaces/agentic-dev-harness-open-source-worktree/scripts/proofrails-check); ./scripts/bootstrap-harness --dry-run "$TMP"; ./scripts/harness-check --target "$TMP"; printf 'SMOKE_TMP=%s\n' "$TMP"
rg -n "Agentic Dev Harness|agentic-dev-harness|agentic-dv-harness|\bharness\b|\bHarness\b|bootstrap-harness|harness-check|harness-lint" README.md README.zh-CN.md README.fr.md docs examples templates skill scripts LICENSE
rg -n "Build AI development guardrails in 10 minutes|10 分钟构建 AI 开发护栏|garde-fous.*10 minutes" README.md README.zh-CN.md README.fr.md
```

## Results

- `./scripts/proofrails-lint`: passed.
- `./scripts/proofrails-check`: passed.
- `./scripts/harness-lint`: passed through compatibility wrapper.
- `./scripts/harness-check`: passed through compatibility wrapper.
- Bootstrap smoke test: passed.
  - Dry-run created no `.agentic` directory.
  - Bootstrap created `.agentic/changes`, `.agentic/runs`, `planwithfile`, `.evidence`, `AGENTS.md`, and `CLAUDE.md`.
  - `proofrails-check --target "$TMP"` passed.
  - Bare `proofrails-check` from inside the bootstrapped target passed.
  - Legacy `bootstrap-harness --dry-run "$TMP"` and `harness-check --target "$TMP"` passed.
- Brand scan: remaining old names are limited to documented compatibility aliases and wrapper script references.
- Tagline scan: `README.md` preserves `Build AI development guardrails in 10 minutes.`

## Remaining risks

- Repository remote URLs in docs assume the future GitHub location `https://github.com/proofrails/proofrails.git`; this was not network-verified.
- The installed workspace directory remains `.agentic/` for compatibility with existing workflow artifacts.
- Existing evidence files from earlier reviews still describe the pre-rename project history; they were not rewritten.
