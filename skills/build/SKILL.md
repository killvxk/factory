---
name: build
description: "Implements features using strict TDD with parallel worktree isolation. Use when implementing any feature, fixing any bug, or changing any behavior after a plan exists. Triggers: build, implement, code this, TDD, develop, write the code. NOT for design (use /think) or review (use /check)."
version: 1.0.0
---

# /build — Red-Green-Refactor, No Exceptions

## Goal

Implement code using strict Test-Driven Development in isolated worktrees.
For multi-task plans, dispatch parallel subagents with two-stage review gates.

## Iron Law

```
NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST
```

If code was written before its test: DELETE IT and start over.
This is not a suggestion. This is the rule.

## Constraints

- Every feature starts with a FAILING test (RED)
- Minimum code to make it pass (GREEN)
- Refactor only after green (REFACTOR)
- Never implement more than ~100 lines before testing
- Each subagent works in an isolated git worktree

## Single-Task Mode

When implementing a single task or small change:

1. Write the test. Run it. Confirm it FAILS (RED).
2. Write the minimum implementation to pass (GREEN).
3. Run tests. Confirm ALL pass.
4. Refactor if needed. Run tests again.
5. Commit with descriptive message.

## Multi-Task Mode (TeamCreate)

When a plan has 3+ independent tasks:

1. Create a pre-development checkpoint:
   ```bash
   git add -u && git commit -m "Pre-development checkpoint" --allow-empty
   MAIN_BRANCH=$(git branch --show-current)
   ```

2. For each independent task, dispatch a subagent in its own worktree:
   - Create worktree: `git worktree add ../worktree-dev-{N} -b dev-branch-{N}`
   - The subagent MUST invoke the TDD cycle (not skip to implementation)
   - The subagent reports: Status, Branch, Worktree path, Tests passed/total

3. Two-stage review per subagent (from superpowers empirical finding):
   - **Stage 1: Spec compliance** — Does the code match the task requirements?
   - **Stage 2: Code quality** — Is the code clean, tested, maintainable?
   - Stage 1 MUST pass before Stage 2 begins. Never conflate them.

4. Integration:
   - Merge branches in dependency order
   - Run full build + test suite on merged code
   - Clean up worktrees

## Failure Protocol

- Single subagent fails (independent): retry up to 2 times
- 3+ subagents fail or share a blocker: STOP, report to user, suggest revisiting /plan
- Same test fails after fix: re-read execution path from scratch (do not patch again)

## Verification

After all code is written:
- Run the project's build command. Paste last 10 lines as evidence.
- Run the project's test command. Paste test summary line as evidence.
- If either fails: fix before claiming completion (Iron Law 3).

## Verification

After all code is written, consult `references/verification-checklists.md` for the /build checklist.

## Gotchas

- Wrote production code before the test — must delete and restart
- Test passes but for the wrong reason (tautological assertion) — verify the test actually tests behavior
- Worktree left behind after merge — always run `git worktree prune`
- Merged branches in wrong order (dependency violation) — check dependency graph from /plan
- Skipped Stage 1 review, jumped to code quality — spec bugs survive

## Anti-Rationalizations

| You might think... | But actually... |
|--------------------|-----------------|
| "This function is too simple to test" | Simple functions compose into complex systems. Test it. |
| "I'll write integration tests instead" | Unit tests are faster and more precise. Write both. |
| "Mocking is too hard for this" | If it's hard to mock, the coupling is the bug. |
| "The test infrastructure isn't set up yet" | Set it up. That's part of the task. |
| "TDD slows me down" | TDD slows your typing. It speeds your shipping. |
| "This is just a config change" | Config changes break production. Test the config. |
| "I'll add tests after the PR" | After never comes. RED before GREEN. Always. |
| "The tests pass, so the code is correct" | Tests prove what they test, not what they don't. Check coverage. |
| "I know this works, I tested it manually" | Manual tests evaporate. Automated tests persist. Write it down. |
| "This is a refactor, behavior doesn't change" | Prove it: tests before AND after. Identical results = safe refactor. |
| "The existing code doesn't have tests either" | That's not an excuse. Add tests for what you touch. |
| "Worktree setup takes too long" | 3 commands, 10 seconds. Merge conflicts take hours. |
