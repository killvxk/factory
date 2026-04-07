---
name: plan
description: "Creates zero-ambiguity implementation plans with exact file paths and verification commands. Use when a direction is approved and you need to break work into tasks. Triggers: plan, break this down, create tasks, implementation plan, how should we build this. NOT for exploring ideas (use /think) or executing (use /build)."
version: 1.0.0
---

# /plan — Zero-Ambiguity Task Lists

## Goal

Translate an approved design into a structured implementation plan where every task
has exact file paths, acceptance criteria, and verification commands.
A good plan can be executed by a different person (or agent) without asking questions.

## Constraints

- Every task MUST have: file paths, acceptance criteria, verification command
- No placeholders: "TBD", "TODO", "implement later" are forbidden
- Parallel tasks explicitly marked with no blocking dependencies between them
- Plan must be reviewed (self-review checklist) before presenting to user

## Process

1. Read the approved direction from /think output
2. Consult `docs/solutions/` for relevant past implementations
3. Identify all files that need to change (grep, glob, read)
4. Break into tasks:
   - Each task is independently testable
   - Mark parallel tasks explicitly
   - Order dependent tasks by dependency
5. For each task, specify:
   - **What**: 1-2 sentence description
   - **Files**: exact paths
   - **Acceptance**: what "done" looks like
   - **Verify**: exact command to confirm (e.g., `npm test -- --grep "user auth"`)
6. Self-review against checklist before presenting

## Task Sizing

- Target: 6+ tasks for TeamCreate parallel execution
- If fewer than 3: probably not worth /plan, just /build directly
- If complexity doesn't split into 6, decompose along these axes:
  core logic, boundary handling, error handling, unit tests, type definitions, integration tests

## Output Format

```markdown
## Implementation Plan: {feature name}

### Task 1: {description}
- Files: `src/auth/middleware.ts`, `src/auth/types.ts`
- Acceptance: JWT validation returns 401 for expired tokens
- Verify: `npm test -- --grep "JWT"`
- Parallel: yes (no dependency on other tasks)

### Task 2: {description}
- Files: `src/api/routes/user.ts`
- Acceptance: GET /me returns current user profile
- Verify: `curl -H "Authorization: Bearer $TOKEN" localhost:3000/me`
- Depends on: Task 1
```

## Plan Review

After drafting the plan, run a self-review AND optionally invoke `factory:check` in headless mode
on the plan document itself (document-review pattern from compound-engineering).
If headless review is overkill for the scope, at minimum complete this self-review:

- [ ] Every task has files, acceptance, verify
- [ ] No circular dependencies
- [ ] Parallel tasks have no shared mutable state
- [ ] Verification commands are real (not "check manually")
- [ ] Total scope matches the approved direction (no scope creep)

## Verification

Before closing, consult `references/verification-checklists.md` for the /plan checklist.

## Anti-Rationalizations

| You might think... | But actually... |
|--------------------|-----------------|
| "The plan is in my head, no need to write it down" | If you can't write it, you can't execute it. Write it. |
| "This is too small for a formal plan" | If it has 3+ tasks, it needs a plan. Small plans are fast to write. |
| "Verification commands are obvious" | Write them. Obvious to you, not to the Dev agent in a fresh worktree. |
| "Dependencies are clear from context" | Mark them explicitly. Context is lost between sessions. |
| "I'll figure out the file paths during implementation" | Grep now. Wrong paths waste entire task cycles. |
| "The spec covers everything" | Specs say WHAT. Plans say HOW. Both are needed. |

## Gotchas

- Plan included tasks not in the approved direction (scope creep)
- Verification command was "run tests" instead of a specific test filter
- Parallel tasks shared a database migration (ordering bug after merge)
- File paths were guessed without grepping — file didn't exist at that path
