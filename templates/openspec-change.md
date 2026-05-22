# OpenSpec Change Template

Use this template for `.openspec/changes/<change-id>/` or `.agentic/changes/<change-id>/`.

## Directory structure

```text
<change-id>/
├── proposal.md
├── design.md
├── tasks.md
└── specs/
    └── <capability>/
        └── spec.md
```

## proposal.md

```markdown
# Proposal: <change-id>

## Why

<One paragraph explaining the motivation. What problem does this solve? What opportunity does it capture?>

## What changes

<Bullet list of concrete changes. Be specific about files, modules, APIs.>

## Non-goals

<What is explicitly out of scope for this change.>

## Impact

<Who or what is affected: users, callers, downstream services, dashboards, docs.>

## Risks

<What could go wrong. High-risk contracts touched. Mitigation for each.>

## Rollback

<Step-by-step rollback plan. Include commands where possible.>
```

## design.md

```markdown
# Design: <change-id>

## Current state

<How it works today. Include relevant code paths, data flow, config.>

## Target state

<How it will work after this change. Include architecture decisions.>

## Key decisions

1. <Decision 1> — <Why this over alternatives>
2. <Decision 2> — <Why this over alternatives>

## Alternatives rejected

- <Alternative A>: <Why rejected>
- <Alternative B>: <Why rejected>

## Data flow / control flow

<Diagram or description of how data moves through the system after the change.>

## Risk matrix

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| <risk 1> | Low/Med/High | Low/Med/High | <mitigation> |
| <risk 2> | Low/Med/High | Low/Med/High | <mitigation> |

## Verification strategy

<How will we know this works? Tests, manual steps, monitoring, rollout plan.>
```

## tasks.md

```markdown
# Tasks: <change-id>

- [ ] Discovery complete
- [ ] Spec scenarios approved
- [ ] Tests or characterization evidence added
- [ ] Implementation slice 1: <description>
- [ ] Implementation slice 2: <description>
- [ ] Implementation slice N: <description>
- [ ] Review against spec
- [ ] Verification evidence captured
- [ ] Archive/update durable knowledge
```

## specs/<capability>/spec.md

```markdown
# Spec: <capability>

## ADDED Requirements

### Requirement: <behavior description>

#### Scenario: <scenario name>
- Given <precondition>
- When <action>
- Then <expected outcome>

## MODIFIED Requirements

### Requirement: <behavior description>

#### Scenario: <scenario name>
- Given <precondition>
- When <action>
- Then <expected outcome>

## REMOVED Requirements

### Requirement: <behavior description>

**Reason:** <why removed>
**Migration:** <how callers should adapt>
```
