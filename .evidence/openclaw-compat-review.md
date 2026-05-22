# OpenClaw Cross-Agent Compatibility Review

**Reviewer:** OpenClaw (cross-agent compatibility reviewer agent)  
**Date:** 2026-05-23  
**Repository:** `agentic-dev-harness-open-source-worktree`  
**Current state:** Post-Codex-review fixes (contract drift fixed, scanner false negatives fixed)

## Verdict

**APPROVED_WITH_NOTES**

The harness is broadly usable by multiple AI coding agents — Claude Code, Codex, OpenClaw, Hermes, and generic Markdown-skill agents — with minor Claude-specific naming conventions that do not block but do create small friction for non-Claude agents.

---

## Agent-by-Agent Compatibility

| Agent | Compatible? | Notes |
|---|---|---|
| **Claude Code** | ✅ Full | Native target — slash commands, `CLAUDE.md`, `~/.claude/skills/` paths all work |
| **OpenClaw/Hermes** | ✅ Full with setup adaptation | `AGENTS.md` + `skill/SKILL.md` both present; YAML frontmatter has `hermes` metadata tags; `/command` routing works. Install path must be adjusted from `~/.claude/skills/` |
| **Codex CLI** | ✅ Yes | Markdown-based skill loading works; natural-language invocation of the skill works as fallback; `.agentic/` tree is format-agnostic |
| **Generic Markdown agents** | ✅ Yes | Any agent that reads Markdown instructions from `AGENTS.md` or `CLAUDE.md` can follow this workflow; no proprietary format dependencies |

---

## Hard Requirement Checks

### 1. No single-agent proprietary dependency

**Status: PASS WITH NOTES**

- `skill/SKILL.md` references gstack skills (Claude Code ecosystem) but all have **explicit degraded-mode fallbacks** documented in the same file.
- Optional integrations (OpenSpec, gstack, Superpowers, GitNexus, gbrain) are documented with "without it" columns in both README and SKILL.md.
- **Note:** The primary agent instruction file is named `CLAUDE.md` — a Claude-specific convention. However, `AGENTS.md` is also provided as a generic alternative, and OpenClaw/Hermes agents prioritize `AGENTS.md` first.
- **Note:** The quickstart shows install paths only for `~/.claude/skills/`. No OpenClaw (`~/.openclaw/workspace/skills/`), Codex, or other install paths are documented.
- **Note:** The `/agentic-dev-harness` slash-command invocation works with OpenClaw but is a Claude Code convention; Codex CLI uses natural-language task descriptions instead.

### 2. Installed target contract consistency

**Status: PASS**

The contract is fully consistent across all files:

| Artifact | `bootstrap-harness` creates | `harness-check` validates | README/docs promise |
|---|---|---|---|
| `.agentic/changes/` | ✅ | ✅ | ✅ |
| `.agentic/runs/` | ✅ | ✅ | ✅ |
| `planwithfile/` | ✅ | ✅ | ✅ |
| `.evidence/` | ✅ | ✅ | ✅ |
| `AGENTS.md` | ✅ | ✅ | ✅ |
| `CLAUDE.md` | ✅ | ✅ | ✅ |

All docs (README, quickstart, concepts, recipes, troubleshooting, templates) consistently reference this contract. Verified by reading all EN/ZH/FR docs.

### 3. 10-minute bootstrap for new users

**Status: PASS**

- `bootstrap-harness` completes in < 1 second (verified).
- `harness-check` completes in < 1 second (verified).
- Quickstart has 6 clear steps: prerequisites → install → bootstrap → validate → use → lint.
- Total time including doc reading: ~5–10 minutes.
- Bootstrap into a temp directory and `harness-check` validation both pass cleanly.

### 4. OpenClaw/Hermes agents can follow AGENTS.md and skill/SKILL.md

**Status: PASS WITH NOTES**

**What works:**
- `skill/SKILL.md` includes Hermes metadata tags in YAML frontmatter — explicit OpenClaw compatibility.
- `templates/AGENTS.md` defines clean, agent-agnostic role rules (lead, reviewer, tester).
- `skill/SKILL.md` is a single-entry coordination skill that OpenClaw's skill routing handles natively.
- The 12-stage state machine is documented in plain Markdown with no agent-specific logic.

**Friction points:**
1. A novel OpenClaw user following the quickstart will not find instruction for OpenClaw's skill path (`~/.openclaw/workspace/skills/`). They must infer the adaptation.
2. `CLAUDE.md` as the primary project-instructions filename is Claude-branded. OpenClaw agents do read it, but it signals that Claude Code is the "first-class" agent. This is cosmetic but real for branding-sensitive teams.
3. The `/agentic-dev-harness` invocation pattern is a Claude Code convention. OpenClaw supports this natively, so no functional gap — but agents like Codex use natural language instead.

### 5. Safe validation scripts

**Status: PASS**

All three scripts run successfully with zero issues:

- `./scripts/harness-lint` → All checks passed.
- `./scripts/harness-check` (source mode) → All source checks passed.
- `TMP=$(mktemp -d); ./scripts/bootstrap-harness "$TMP"; (cd "$TMP" && ./scripts/harness-check)` → Bootstrap creates the full contract; target-mode check passes all 6 items.

Tested on macOS (Darwin arm64, zsh). Scripts are POSIX-compliant with only standard Unix dependencies (git, grep, find, mkdir, touch, cp, cat, mktemp).

No files were modified during checks (harness-check and harness-lint are read-only; bootstrap-harness was tested against a disposable temp directory).

---

## Detailed Findings

### Positive

1. **No blocked internal patterns** — `harness-lint` and `harness-check` both scanned all tracked files and found zero occurrences of any forbidden term. The scanner implementation uses hex-encoded ANSI-C quoting (`$'\xHH'`), so the scanner scripts themselves pass their own scan. This is a well-designed security pattern.

2. **Zero hard dependencies** — The harness works with only `git` and a POSIX shell. Every optional integration has a documented fallback. This is the single strongest cross-agent compatibility feature.

3. **Evidence-driven workflow** — The 12-stage state machine with explicit artifact output means any agent, regardless of internal architecture, can participate at any stage. The disk-based contract is agent-agnostic.

4. **Clear role separation** — `AGENTS.md` defines lead/reviewer/tester roles with clean boundaries. This maps naturally to OpenClaw's sub-agent spawning pattern and Codex's multi-agent workflows.

5. **Multilingual docs** — English, Chinese, and French docs are structurally consistent, with the same contract references in all three languages.

### Minor Issues (not blocking, worth addressing)

| # | Issue | Impact | Suggested Fix |
|---|---|---|---|
| 1 | Quickstart only shows `~/.claude/skills/` install path | OpenClaw/Codex users must adapt manually | Add one line: "For OpenClaw: copy to `~/.openclaw/workspace/skills/agentic-dev-harness/SKILL.md`" |
| 2 | Primary instruction file is `CLAUDE.md` | Claude-branded naming suggests Claude-only | Already mitigated by `AGENTS.md` exiting alongside; consider noting in README that both files serve the same purpose |
| 3 | `skill/SKILL.md` references `~/.claude/skills/gstack/SKILL.md` in probe commands | OpenClaw/Hermes agents would not find gstack at this path | Already mitigated by degraded-mode design; probe script is informative not gating |
| 4 | No OpenClaw-specific probe in Stage 0 | Missing the chance to detect OpenClaw-specific features | Low severity — the harness works without detection |
| 5 | `harness-lint` and `harness-check` have no self-test | Scanner regression risk for forbidden-term detection | Add a `--self-test` mode that seeds a known forbidden term and confirms detection |

---

## Conclusion

This harness is genuinely cross-agent compatible. The architecture decisions — single-entry skill, disk-based artifacts, zero hard dependencies, explicit degraded-mode fallbacks — make it work across Claude Code, Codex, OpenClaw, Hermes, and generic Markdown agents.

The noted issues are about documentation breadth (only one install path shown) and naming conventions (CLAUDE.md as the primary file), not about functional compatibility. No agent is blocked from using this harness.

**Verdict: APPROVED_WITH_NOTES**

The notes are actionable but non-blocking. The most impactful single fix would be adding a non-Claude install path to the quickstart so OpenClaw and Codex users can bootstrap without manual path inference.
