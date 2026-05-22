# ProofRails Final Verification

## Summary

ProofRails is prepared as a clean open-source package with no legacy project-name aliases, no legacy script wrappers, and no internal platform references.

## Public interface

- Skill name: `proofrails`
- Canonical invocation: `/proofrails`
- Scripts:
  - `scripts/proofrails-bootstrap`
  - `scripts/proofrails-check`
  - `scripts/proofrails-lint`

## Verification commands

```bash
./scripts/proofrails-lint
./scripts/proofrails-check
git diff --check
TMP=$(mktemp -d)
./scripts/proofrails-bootstrap --dry-run "$TMP"
test ! -e "$TMP/.proofrails"
./scripts/proofrails-bootstrap "$TMP"
./scripts/proofrails-check --target "$TMP"
(cd "$TMP" && <repo>/scripts/proofrails-check)
```

## Expected result

- Source lint passes.
- Source integrity check passes.
- Bootstrap dry-run creates no files.
- Bootstrap creates `.proofrails/changes`, `.proofrails/runs`, `planwithfile`, `.evidence`, `AGENTS.md`, and `CLAUDE.md`.
- Target checks pass both from the source repository and from inside the installed target directory.
- Old-name scan passes.
- Forbidden-term scan passes.
