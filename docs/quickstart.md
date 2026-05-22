# Quickstart

Get the harness running on your project in under 10 minutes.

## Step 1: Verify prerequisites

```bash
git --version          # >= 2.0
echo $SHELL            # bash, zsh, or dash
```

## Step 2: Install the harness

**Option A: Clone the repo**

```bash
git clone https://github.com/agentic-dev-harness/harness.git /tmp/harness
cp -r /tmp/harness/{skill,scripts,templates,docs,examples} ./
```

**Option B: Copy just the skill**

```bash
# Claude Code
mkdir -p ~/.claude/skills/agentic-dev-harness
cp harness/skill/SKILL.md ~/.claude/skills/agentic-dev-harness/SKILL.md

# OpenClaw / Hermes-style Markdown skills
mkdir -p ~/.openclaw/workspace/skills/agentic-dev-harness
cp harness/skill/SKILL.md ~/.openclaw/workspace/skills/agentic-dev-harness/SKILL.md
```

For other Markdown-skill agents, copy `skill/SKILL.md` to that tool's skill directory.

**Option C: Use the bootstrap script from this repo**

If you have already cloned this repo, run the bootstrap script directly:

```bash
./scripts/bootstrap-harness
```

## Step 3: Bootstrap your project

```bash
# Dry-run first — see what will be created without touching files
./scripts/bootstrap-harness --dry-run

# Apply
./scripts/bootstrap-harness
```

Expected output:

```
[harness] Target: /path/to/your-project
[harness] Created .agentic/changes/
[harness] Created .agentic/runs/
[harness] Created planwithfile/
[harness] Created .evidence/
[harness] Wrote AGENTS.md (from template)
[harness] Wrote CLAUDE.md (from template)
[harness] Done. Run ./scripts/harness-check to verify.
```

## Step 4: Validate

```bash
./scripts/harness-check
```

Expected output:

```
[harness-check] Verifying installed harness...
[harness-check] Target: /path/to/your-project

[harness-check]   .agentic/changes/: OK
[harness-check]   .agentic/runs/: OK
[harness-check]   planwithfile/: OK
[harness-check]   .evidence/: OK
[harness-check]   CLAUDE.md: OK
[harness-check]   AGENTS.md: OK

[harness-check] All target checks passed.
```

## Step 5: Use the skill

Invoke the skill in your AI coding tool:

```
/agentic-dev-harness in ./ fix the login timeout bug. Start with brownfield discovery. Wait for approval before editing code. Success criteria: regression test passes.
```

## Step 6: Lint regularly

```bash
./scripts/harness-lint
```

This scans for common issues: missing required files and forbidden terms.

## Next steps

- Read [Concepts](./concepts.md) to understand the architecture.
- Read [Recipes](./recipes.md) for common workflows.
- Check [Troubleshooting](./troubleshooting.md) if something goes wrong.
