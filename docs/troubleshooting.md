# Troubleshooting

Common issues and how to resolve them.

## `harness-check` reports missing files

**Symptom:**

```
[harness-check]   CLAUDE.md: MISSING
[harness-check]   AGENTS.md: MISSING
```

**Fix:**

Run bootstrap to create missing files:

```bash
./scripts/bootstrap-harness
```

Files that already exist will not be overwritten unless you pass `--force`.

## `harness-lint` reports forbidden terms

**Symptom:**

```
[harness-lint] FORBIDDEN: found forbidden term in docs/concepts.md:42
```

**Fix:**

Remove the forbidden term from the flagged file. The harness-lint script checks against a built-in list of platform-internal references that must not appear in the open-source repository.

## Bootstrap script overwrites my files

**Symptom:**

You ran `./scripts/bootstrap-harness --force` and your custom CLAUDE.md was replaced.

**Fix:**

By default, bootstrap does **not** overwrite existing files. Only `--force` does. If you need selective updates:

1. Run `--dry-run` first to see what would change.
2. Back up your custom files.
3. Run with `--force` and re-apply your customizations.
4. Consider maintaining your customizations in a separate file that CLAUDE.md sources.

## Harness creates directories you do not want

**Symptom:**

The harness created `.agentic/` but your team uses OpenSpec.

**Fix:**

The harness detects `.openspec/` and uses it if present. If you prefer OpenSpec:

```bash
openspec init
```

The harness can use `.openspec/changes/` for change proposals while keeping `.agentic/runs/` for review reports and run evidence. Keep `.agentic/runs/` in place so `harness-check` can validate the installed harness.

If your team standardizes on OpenSpec, configure your AI tool to write change drafts to `.openspec/changes/`, but do not delete the whole `.agentic/` directory. It remains the local run/evidence home for this harness.

## Skill is not recognized by my AI tool

**Symptom:**

Typing `/agentic-dev-harness` does nothing.

**Fix:**

1. Verify the skill file exists at the correct path for your tool:
   ```bash
   # Claude Code
   ls ~/.claude/skills/agentic-dev-harness/SKILL.md

   # OpenClaw / Hermes-style Markdown skills
   ls ~/.openclaw/workspace/skills/agentic-dev-harness/SKILL.md
   ```

2. For Claude Code, check that skills are enabled in settings.

3. For other AI tools, consult their documentation for skill loading. The harness skill is a standard Markdown file with YAML frontmatter — any tool that supports Markdown-based skills should recognize it.

4. As a fallback, you can paste the contents of `skill/SKILL.md` directly into your AI tool's instructions.

## Discovery takes too long on a large codebase

**Symptom:**

The harness is reading hundreds of files during brownfield discovery.

**Fix:**

The harness tries to be efficient but large codebases need guidance:

1. Provide more specific entry points in your invocation:
   ```
   /agentic-dev-harness in ./my-project fix the payment timeout.
   Entry point: src/payment/processor.ts. Existing tests: src/payment/__tests__/.
   ```

2. If your project has a CLAUDE.md or AGENTS.md, the harness reads it first to understand project structure.

3. For very large monorepos, consider running the harness scoped to a specific module or service.

## Approval gate is skipped

**Symptom:**

The AI edited source code without asking for approval.

**Fix:**

This is an AI behavior issue, not a harness issue. The harness SKILL.md explicitly requires approval before source edits. If the AI skips this:

1. Remind it: "You must present the approval gate before editing source code."
2. Add a stronger project rule in CLAUDE.md:
   ```markdown
   # Hard rule
   NEVER edit source code without explicit user approval.
   Present scope, risks, and plan first.
   ```
3. Use your AI tool's permission system to require approval for file writes.

## Evidence directory is filling up

**Symptom:**

`.evidence/` has many large files.

**Fix:**

Evidence files are immutable but you can archive old ones:

```bash
# Archive evidence older than 90 days
find .evidence/ -type f -mtime +90 -exec mv {} .evidence/archive/ \;

# Or use git to track only recent evidence
echo ".evidence/archive/" >> .gitignore
```

The `.evidence/` directory is intended for current and recent evidence. Old evidence should still exist in git history and in archived changes.
