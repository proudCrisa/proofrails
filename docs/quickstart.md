# Quickstart

Get ProofRails running on your project in under 10 minutes.

## Step 1: Verify prerequisites

```bash
git --version          # >= 2.0
echo $SHELL            # bash, zsh, or dash
```

## Step 2: Install ProofRails

**Option A: Clone the repo**

```bash
git clone https://github.com/proofrails/proofrails.git /tmp/proofrails
cp -r /tmp/proofrails/{skill,scripts,templates,docs,examples} ./
```

**Option B: Copy just the skill**

```bash
# Claude Code
mkdir -p ~/.claude/skills/proofrails
cp proofrails/skill/SKILL.md ~/.claude/skills/proofrails/SKILL.md

# OpenClaw / Hermes-style Markdown skills
mkdir -p ~/.openclaw/workspace/skills/proofrails
cp proofrails/skill/SKILL.md ~/.openclaw/workspace/skills/proofrails/SKILL.md
```

For other Markdown-skill agents, copy `skill/SKILL.md` to that tool's skill directory.

**Option C: Use the bootstrap script from this repo**

If you have already cloned this repo, run the bootstrap script directly:

```bash
./scripts/proofrails-bootstrap
```

## Step 3: Bootstrap your project

```bash
# Dry-run first — see what will be created without touching files
./scripts/proofrails-bootstrap --dry-run

# Apply
./scripts/proofrails-bootstrap
```

Expected output:

```
[proofrails] Target: /path/to/your-project
[proofrails] Created .agentic/changes/
[proofrails] Created .agentic/runs/
[proofrails] Created planwithfile/
[proofrails] Created .evidence/
[proofrails] Wrote AGENTS.md (from template)
[proofrails] Wrote CLAUDE.md (from template)
[proofrails] Done. Run ./scripts/proofrails-check to verify.
```

## Step 4: Validate

```bash
./scripts/proofrails-check
```

Expected output:

```
[proofrails-check] Verifying installed ProofRails...
[proofrails-check] Target: /path/to/your-project

[proofrails-check]   .agentic/changes/: OK
[proofrails-check]   .agentic/runs/: OK
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

## Step 6: Lint regularly

```bash
./scripts/proofrails-lint
```

This scans for common issues: missing required files and forbidden terms.

## Next steps

- Read [Concepts](./concepts.md) to understand the architecture.
- Read [Recipes](./recipes.md) for common workflows.
- Check [Troubleshooting](./troubleshooting.md) if something goes wrong.
