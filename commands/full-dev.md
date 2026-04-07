---
description: "End-to-end feature development pipeline. Use when implementing features requiring design validation, parallel TDD, multi-persona review, and knowledge capture. Triggers: full-dev, 全功能开发, 端到端, Team开发. NOT for bug fixes (use /hunt), single-file changes, or research."
---

# Full Development Pipeline

Strictly sequential phases. Each phase must satisfy Exit before proceeding.

**Feature**: $ARGUMENTS

---

## Essential Principles

1. **Leader does not write code** — Coordinator dispatches and monitors only
2. **Isolated worktrees** — Each Dev works in its own worktree, no filesystem conflicts
3. **Review after merge** — Review the complete merged result, not individual fragments
4. **Fail loud** — Any step failure must be reported immediately, never silently skipped
5. **Evidence before completion** — Every phase requires proof, not assertions

---

## Pre-flight

1. If `$ARGUMENTS` is empty, ask user for feature requirements and stop
2. Confirm these skills are available: `factory:think`, `factory:plan`, `factory:build`, `factory:check`, `factory:compound`
3. Any skill unavailable: list missing items and stop

---

## Phase 1: Design Validation

**Entry:** Pre-flight passed
**Action:** Invoke Skill tool: `factory:think` with `$ARGUMENTS`

Follow the think skill's guidance to:
- Check docs/solutions/ for prior art
- Explore requirements with user
- Propose 2-3 approaches with trade-offs
- Attack own recommendation

**Exit [GATE 1]:** User explicitly confirms direction

---

## Phase 2: Implementation Plan

**Entry:** Phase 1 confirmed
**Action:** Invoke Skill tool: `factory:plan`

Follow the plan skill's guidance to:
- Break work into 6+ independent tasks
- Each task has file paths, acceptance criteria, verification command
- Mark parallel tasks explicitly
- Self-review plan against checklist

**Exit [GATE 2]:** User confirms task list and dependencies

---

## Phase 3: Team Development

### 3.1 Environment + Team

**Entry:** Phase 2 confirmed
**Actions (main session):**

```bash
if ! git rev-parse --git-dir > /dev/null 2>&1; then git init; fi
git status --short
git add -u && git commit -m "Pre-development checkpoint" --allow-empty
MAIN_BRANCH=$(git branch --show-current)
echo "Main branch: $MAIN_BRANCH"
git worktree prune
```

Create team via TeamCreate. All members use same model as current session.

**Required roles:**

| Role | Name | Count | Responsibility |
|------|------|-------|----------------|
| Leader | Coordinator | 1 | Dispatch and monitor. NO code writing. |
| Developer | Dev-1..N | >=6 | Isolated worktree + TDD |
| Integrator | Integrator | 1 | Merge branches, resolve conflicts |
| Reviewer | Reviewer | 1 | Multi-persona code review |
| Quality | QualityGate | 1 | Fix review findings |

**Exit:** Team created, all 5 role types present

### 3.2 Parallel TDD

**Entry:** Team ready
**Action:** Coordinator sends task prompts to all Devs simultaneously.

Each Dev MUST:
1. Create worktree: `git worktree add ../worktree-dev-{N} -b dev-branch-{N}`
2. Verify with `pwd` — must show worktree path
3. Follow TDD Iron Law: write failing test FIRST, then implement
4. Two-stage self-review: spec compliance, then code quality
5. Commit and report to Coordinator

**Dev Report Format:**
```
## Dev-{N} Report
- Status: success | failure
- Branch: dev-branch-{N}
- Worktree: {pwd output}
- Tests: {passed}/{total}
- Files Changed: {list}
- Blockers: none | {description}
```

**Coordinator validates:** Worktree path contains `worktree-dev-`, tests pass.

**Failure handling:**
- Single Dev failure: retry up to 2 times
- 3+ Devs fail: terminate team, report to main session, suggest revisiting Phase 2
- Dev timeout: mark as failure, continue with remaining

**Exit:** All Devs reported success (or failures handled)

### 3.3 Integration

**Entry:** All Devs complete
**Action:** Coordinator sends merge task to Integrator.

Integrator MUST:
1. `git checkout {MAIN_BRANCH}`
2. Merge branches in dependency order: `git merge dev-branch-{i} --no-edit`
3. Resolve conflicts preserving both sides' functionality
4. Run build command — paste last 10 lines as evidence
5. Run test command — paste summary as evidence
6. Clean up worktrees (only if no uncommitted changes)
7. Report to Coordinator

**Exit:** All branches merged, build passes, tests pass

### 3.4 Code Review

**Entry:** Integration complete
**Action:** Coordinator sends review task to Reviewer.

Reviewer MUST invoke Skill tool: `factory:check` in headless mode.
Scope: all changes since "Pre-development checkpoint".

**Reviewer Report:**
```
## Reviewer Report
- factory:check invoked: yes | no
- Status: pass | has findings
- P0: {count}
- P1: {count}
- P2: {count}
- Recommendation: proceed | needs fix | needs redesign
```

**Coordinator validates:** `factory:check invoked` must be `yes`.

**Exit:** Review complete, recommendation is not `needs redesign`

### 3.5 Quality Fix

**Entry:** Review complete (always runs, even if 0 findings — independent verification)
**Action:** Coordinator sends fix task to QualityGate.

QualityGate MUST:
1. Review all P0 and P1 findings
2. Fix P0 and P1 (P2 deferred unless trivial)
3. Run build + tests after each fix round
4. Maximum 5 rounds — if not converged, pause and report
5. Report to Coordinator

**Exit:** P0 = 0, build passes, tests pass

### 3.6 Team Report

**Entry:** QualityGate complete
**Action:** Coordinator reports to main session:

```
## Development Report
- Parallel dev: {N} tasks, {success}/{total}
- Integration: {status}, {conflicts} conflicts
- Review: P0:{x} P1:{y} P2:{z}
- Quality fix: {fixed}, {remaining}
- Build: pass | fail
- Tests: {passed}/{total}
```

**Exit:** Main session receives report, team exits

---

## Phase 4: Final Verification

**Entry:** Team exited, report received
**Actions (main session):**

1. Run full build — confirm pass
2. Run all tests — confirm pass
3. Check report:
   - P0 remaining > 0 → STOP, manual intervention
   - P1 remaining > 2 → evaluate acceptability
4. Confirm cleanup:
   ```bash
   git worktree list
   git branch --list 'dev-branch-*'
   ```
5. Present results to user

**Exit [GATE 3]:** Build passes + tests pass + no P0 + environment clean

---

## Phase 5: Ship + Knowledge Capture

**Entry:** Phase 4 verified
**Actions:**

1. Invoke Skill tool: `factory:ship` — present 4 options (merge/PR/keep/discard)
2. After ship action, invoke Skill tool: `factory:compound` — capture:
   - Core problem and solution
   - Key design decisions
   - Collaboration issues and resolutions
   - Reusable patterns

**Exit:** Knowledge document saved, feature shipped or PR created

---

## Failure Protocol

| Scenario | Response |
|----------|----------|
| Single Dev failure (independent) | Retry up to 2x |
| 3+ Dev failures or shared blocker | Terminate team, revisit Phase 2 |
| Dev timeout | Mark failed, continue others |
| Merge conflict unresolvable | Request user intervention |
| Build/test failure after merge | Integrator fixes, up to 2x |
| Architecture-level review issue | Terminate team, revisit Phase 2 |
| Review-fix >5 rounds | Pause, report to Coordinator |
| P0 unfixable | Request user intervention |

## Quick Reference

| Phase | Actor | Action | Gate |
|-------|-------|--------|------|
| Pre | Main | Check dependencies | — |
| 1 | Main | factory:think | G1: User confirms direction |
| 2 | Main | factory:plan | G2: User confirms plan |
| 3.1 | Main | Setup + TeamCreate | Team ready |
| 3.2 | Devs | Parallel TDD | All complete |
| 3.3 | Integrator | Merge + verify | Build + tests pass |
| 3.4 | Reviewer | factory:check | Report complete |
| 3.5 | QualityGate | Fix findings | P0 = 0 |
| 3.6 | Coordinator | Report + exit | Report delivered |
| 4 | Main | Final verification | G3: All clean |
| 5 | Main | factory:ship + factory:compound | Knowledge saved |

## Skill Dependencies

| Skill | Phase |
|-------|-------|
| `factory:think` | 1 |
| `factory:plan` | 2 |
| `factory:build` (TDD Iron Law) | 3.2 (enforced in Dev prompts) |
| `factory:check` | 3.4 |
| `factory:ship` | 5 |
| `factory:compound` | 5 |
