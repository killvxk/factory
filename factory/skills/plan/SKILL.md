---
name: plan
description: "Use when a direction is approved and you need to break work into tasks. Triggers: plan, break this down, create tasks, implementation plan, how should we build this. NOT for exploring ideas (use /think) or executing (use /build)."
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

## Plan Spec Review (mandatory before presenting to user)

After drafting the plan but BEFORE presenting, review the plan itself.

### Self-Review Checklist (all plans)

- [ ] Every task has files, acceptance, verify
- [ ] No circular dependencies
- [ ] Parallel tasks have no shared mutable state
- [ ] Verification commands are real (not "check manually")
- [ ] Total scope matches the approved direction (no scope creep)
- [ ] No TBD/TODO/placeholder in any task

### Structured Plan Review (6+ tasks or Standard/Deep)

For plans with 6+ tasks, review from 4 dimensions:

1. **Completeness**: Does the plan cover ALL requirements from the /think output?
   Walk through the approved direction point by point. Missing coverage = add a task.
2. **Ordering**: Are dependencies correct? Could a task fail because its dependency
   hasn't run yet? Trace the execution order.
3. **Isolation**: Can parallel tasks truly run independently? Check for shared files,
   shared database tables, shared global state.
4. **Verifiability**: Run each verify command mentally. Would it actually catch a failure?
   `npm test` is too broad. `npm test -- --grep "JWT validation"` is specific.

If any dimension fails, fix the plan before presenting.

### Deep Plan Review (10+ tasks or complex architecture)

For large plans, additionally dispatch `factory:check` in headless mode on the plan
document itself (document-review pattern from compound-engineering). This catches
cross-task consistency issues that self-review misses.

## Verification

Before closing, consult `references/verification-checklists.md` for the /plan checklist.

> Anti-rationalizations for this skill: see `references/anti-rationalizations.md` § Plan Phase and Universal Rationalizations.

## Gotchas

- Plan included tasks not in the approved direction (scope creep)
- Verification command was "run tests" instead of a specific test filter
- Parallel tasks shared a database migration (ordering bug after merge)
- File paths were guessed without grepping — file didn't exist at that path
