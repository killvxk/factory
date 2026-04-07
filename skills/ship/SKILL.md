---
name: ship
description: "Guides the final merge, PR, and deployment verification. Use when implementation is complete, tests pass, and you need to integrate the work. Triggers: ship, merge, create PR, deploy, we're done, push this. NOT for review (use /check) or planning (use /plan)."
version: 1.0.0
---

# /ship — Get It Out the Door

## Goal

Safely integrate completed work into the main branch with appropriate
verification at each step. Present exactly 4 options, never open-ended.

## Pre-Ship Verification

Before any ship action:
- [ ] All tests pass on the current branch
- [ ] /check has been run (or headless mode passed)
- [ ] No P0 findings remaining
- [ ] Build succeeds with zero errors

If any fail: STOP. Fix first. Do not ship broken code.

## 4 Structured Options

Present exactly these options (from superpowers finishing-a-development-branch):

1. **Merge locally** — Merge into main/default branch, push
2. **Push and PR** — Push branch, create PR with description
3. **Keep as-is** — Leave branch for later
4. **Discard** — Delete branch and changes (requires typing "discard" to confirm)

Never ask open-ended "what do you want to do?"

## Pre-Launch Checklist (6 Categories)

For option 1 or 2, verify:

### Code Quality
- [ ] No TODO/FIXME without tracking issue
- [ ] No console.log / print debugging left
- [ ] No commented-out code blocks

### Security
- [ ] No hardcoded secrets
- [ ] Input validation on all public endpoints
- [ ] Auth checks on protected routes

### Performance
- [ ] No N+1 queries introduced
- [ ] No synchronous operations in hot paths
- [ ] Bundle size not significantly increased

### Accessibility
- [ ] Keyboard navigation works
- [ ] Screen reader landmarks present
- [ ] Color contrast meets WCAG AA

### Infrastructure
- [ ] Environment variables documented
- [ ] Migrations are reversible
- [ ] Feature flags configured if needed

### Documentation
- [ ] README updated if public API changed
- [ ] CHANGELOG entry added if user-facing change

## Post-Ship

After merge/PR:
- Clean up worktrees: `git worktree prune`
- Delete merged branches: `git branch -d {branch}`
- If deployed: verify production (check health endpoint, run smoke test)

## Gotchas

- Merged without running tests on the merge result (tests passed on branch, failed on merge)
- Created PR but forgot to push the branch first
- Deployed without environment variables configured (got 401/500 in production)
- Forgot to clean up worktrees — disk space accumulated
