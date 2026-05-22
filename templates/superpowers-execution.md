# Superpowers Execution Template

This template emulates the Superpowers execution discipline when Superpowers is not installed. It ensures TDD, systematic debugging, and evidence-based completion.

## Before starting a task

1. Read the task description from `tasks.md`.
2. Confirm exact files that will change.
3. Identify the test that will verify success.
4. State the expected outcome before writing code.

## Task execution loop

```text
Read task
  -> Write/update test (red)
    -> Write minimal implementation (green)
      -> Run test (verify green)
        -> Record evidence
          -> Mark task complete
```

## TDD rules

1. **Write the test first.** The test defines success before implementation exists.
2. **See it fail.** Run the test and confirm it fails for the expected reason.
3. **Minimal implementation.** Write the smallest change that makes the test pass.
4. **Refactor only after green.** Clean up only after all tests pass.
5. **One assertion per test** when practical. Each test tells a single story.

## Debugging rules

1. **Reproduce first.** Can you trigger the bug reliably? If not, add logging and try again.
2. **Narrow the surface.** Bisect commits, isolate modules, minimize inputs until the root cause is obvious.
3. **Hypothesize before fixing.** State what you think is wrong and why. Test the hypothesis.
4. **One fix at a time.** Change one thing, verify, then change the next.
5. **Root cause, not symptom.** The fix should address why the bug exists, not just its visible effect.

## Verification rules

1. **Build passes.** No compilation or packaging errors.
2. **Tests pass.** All existing tests still pass. New tests cover the change.
3. **Lint/typecheck clean.** Zero new warnings.
4. **Manual smoke** when automation is impossible. Record exactly what was tested.
5. **Evidence recorded.** Output saved to `planwithfile/<change-id>/evidence.md`.

## Hard stops

Stop immediately and ask for guidance when:

- **Design no longer fits.** The implementation reveals that the spec is wrong.
- **High-risk contract touched.** A risk boundary from the **Risk boundaries** section in CLAUDE.md is affected.
- **Unexplained failures.** Tests fail for reasons you cannot explain.
- **Scope creep.** The diff is larger than the approved scope.
- **User decision needed.** Two reasonable paths exist and the choice matters.

## Evidence format

```markdown
# Evidence: <task description>

## What was done

<Brief description of the change.>

## Commands run

```bash
<exact commands with output>
```

## Results

<Pass/fail. If fail, explanation and resolution.>

## Artifacts

<Screenshots, log excerpts, API responses.>

## Gaps

<What could not be verified automatically and why.>
```
