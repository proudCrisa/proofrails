# Troubleshooting

Common issues and how to resolve them.

## `proofrails-check` reports missing files

**Symptom:**

```
[proofrails-check]   CLAUDE.md: MISSING
[proofrails-check]   AGENTS.md: MISSING
```

**Fix:**

Run bootstrap to create missing files:

```bash
./scripts/proofrails-bootstrap
```

Files that already exist will not be overwritten unless you pass `--force`.

## `proofrails-lint` reports forbidden terms

**Symptom:**

```
[proofrails-lint] FORBIDDEN: found forbidden term in docs/concepts.md:42
```

**Fix:**

Remove the forbidden term from the flagged file. The proofrails-lint script checks against a built-in list of restricted references that must not appear in the open-source repository.

## Bootstrap script overwrites my files

**Symptom:**

You ran `./scripts/proofrails-bootstrap --force` and your custom CLAUDE.md was replaced.

**Fix:**

By default, bootstrap does **not** overwrite existing files. Only `--force` does. If you need selective updates:

1. Run `--dry-run` first to see what would change.
2. Back up your custom files.
3. Run with `--force` and re-apply your customizations.
4. Consider maintaining your customizations in a separate file that CLAUDE.md sources.

## ProofRails creates directories you do not want

**Symptom:**

ProofRails created `.proofrails/` but your team uses OpenSpec.

**Fix:**

ProofRails detects `.openspec/` and uses it if present. If you prefer OpenSpec:

```bash
openspec init
```

ProofRails can use `.openspec/changes/` for change proposals while keeping `.proofrails/runs/` for review reports and run evidence. Keep `.proofrails/runs/` in place so `proofrails-check` can validate the installed ProofRails.

If your team standardizes on OpenSpec, configure your AI tool to write change drafts to `.openspec/changes/`, but do not delete the whole `.proofrails/` directory. It remains the local run/evidence home for ProofRails.

## Skill is not recognized by my AI tool

**Symptom:**

Typing `/proofrails` does nothing.

**Fix:**

1. Verify the skill file exists at the correct path for your tool:
   ```bash
   # Claude Code
   ls ~/.claude/skills/proofrails/SKILL.md

   # OpenClaw / Hermes-style Markdown skills
   ls ~/.openclaw/workspace/skills/proofrails/SKILL.md
   ```

2. For Claude Code, check that skills are enabled in settings.

3. For other AI tools, consult their documentation for skill loading. ProofRails skill is a standard Markdown file with YAML frontmatter — any tool that supports Markdown-based skills should recognize it.

4. As a fallback, you can paste the contents of `skill/SKILL.md` directly into your AI tool's instructions.

## Discovery takes too long on a large codebase

**Symptom:**

ProofRails is reading hundreds of files during brownfield discovery.

**Fix:**

ProofRails tries to be efficient but large codebases need guidance:

1. Provide more specific entry points in your invocation:
   ```
   /proofrails in ./my-project fix the payment timeout.
   Entry point: src/payment/processor.ts. Existing tests: src/payment/__tests__/.
   ```

2. If your project has a CLAUDE.md or AGENTS.md, ProofRails reads it first to understand project structure.

3. For very large monorepos, consider running ProofRails scoped to a specific module or service.

## Approval gate is skipped

**Symptom:**

The AI edited source code without asking for approval.

**Fix:**

This is an AI behavior issue, not a ProofRails issue. ProofRails SKILL.md explicitly requires approval before source edits. If the AI skips this:

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
