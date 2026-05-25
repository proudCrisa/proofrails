# Quickstart

Get ProofRails running on your project in under 10 minutes.

## Step 1: Verify prerequisites

```bash
git --version          # >= 2.0
echo $SHELL            # bash, zsh, or dash
```

## Step 2: Install ProofRails

**Option A: Clone the repo and run scripts from it (recommended)**

```bash
git clone https://github.com/proudCrisa/proofrails.git /tmp/proofrails
```

Keep this checkout as the source for ProofRails scripts. Source lint validates the ProofRails checkout; target checks validate your installed project.

**Option B: Copy just the skill**

```bash
# Claude Code
mkdir -p ~/.claude/skills/proofrails
cp /tmp/proofrails/skill/SKILL.md ~/.claude/skills/proofrails/SKILL.md

# OpenClaw / Hermes-style Markdown skills
mkdir -p ~/.openclaw/workspace/skills/proofrails
cp /tmp/proofrails/skill/SKILL.md ~/.openclaw/workspace/skills/proofrails/SKILL.md
```

For other Markdown-skill agents, copy `/tmp/proofrails/skill/SKILL.md` to that tool's skill directory. Copying only the skill does not install project scripts; use `/tmp/proofrails/scripts/...` for bootstrap, checks, and source lint.

## Step 3: Bootstrap your project

```bash
# Dry-run first — see what workflow files will be created without touching application/source code
/tmp/proofrails/scripts/proofrails-bootstrap --dry-run /path/to/your-project

# Apply; bootstrap creates ProofRails workflow files only, not application/source code changes
/tmp/proofrails/scripts/proofrails-bootstrap /path/to/your-project
```

Expected output:

```
[proofrails] Target: /path/to/your-project
[proofrails] Created .proofrails/changes/
[proofrails] Created .proofrails/runs/
[proofrails] Created planwithfile/
[proofrails] Created .evidence/
[proofrails] Wrote AGENTS.md (from template)
[proofrails] Wrote CLAUDE.md (from template)
[proofrails] Done. Run /tmp/proofrails/scripts/proofrails-check --target /path/to/your-project to verify.
```

## Step 4: Validate

```bash
/tmp/proofrails/scripts/proofrails-check --target /path/to/your-project
```

Expected output:

```
[proofrails-check] Verifying installed ProofRails...
[proofrails-check] Target: /path/to/your-project

[proofrails-check]   .proofrails/changes/: OK
[proofrails-check]   .proofrails/runs/: OK
[proofrails-check]   planwithfile/: OK
[proofrails-check]   .evidence/: OK
[proofrails-check]   CLAUDE.md: OK
[proofrails-check]   AGENTS.md: OK

[proofrails-check] All target checks passed.
```

## Step 5: Use the skill

Invoke the skill in your AI coding tool:

```
/proofrails in ./ fix the login timeout bug. Start with brownfield discovery. Wait for approval before editing code. Success criteria: regression test passes.
```

## Step 6: Lint the ProofRails checkout

```bash
/tmp/proofrails/scripts/proofrails-lint
```

This source lint scans the full ProofRails checkout for required files and forbidden terms. Use `proofrails-check --target /path/to/your-project` for installed projects.

## Next steps

- Read [Concepts](./concepts.md) to understand the architecture.
- Read [Recipes](./recipes.md) for common workflows.
- Check [Troubleshooting](./troubleshooting.md) if something goes wrong.
