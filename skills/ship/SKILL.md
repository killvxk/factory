---
name: ship
description: "Guides the final merge, PR, and deployment verification. Use when implementation is complete, tests pass, and you need to integrate the work. Triggers: ship, merge, create PR, deploy, we're done, push this. NOT for review (use /check) or planning (use /plan)."
version: 1.0.0
---

# /ship — Get It Out the Door

## Goal

Safely integrate completed work into the main branch with appropriate
verification at each step. Present exactly 4 options, never open-ended.

## Constraints

- Never ship without tests passing on the merged result (not just the branch)
- Never skip the pre-launch checklist for "simple" changes
- Destructive option (discard) requires explicit typed confirmation
- CI must pass before merge — poll and wait, do not assume

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

## CI Wait (from gstack land-and-deploy)

After creating a PR or pushing to main:
1. Check CI status: `gh pr checks {PR_NUMBER}` or `gh run list --limit 1`
2. If CI is running: poll every 30 seconds until complete (max 10 minutes)
3. If CI fails: DO NOT merge. Read the failure output, fix, and re-push.
4. If CI passes: proceed with merge or confirm PR is ready for review.

Never assume CI passed. Always check.

## Post-Ship

After merge/PR:
- Clean up worktrees: `git worktree prune`
- Delete merged branches: `git branch -d {branch}`
- If deployed: verify production (check health endpoint, run smoke test)

## Verification

Before closing, consult `references/verification-checklists.md` for the /ship checklist.

## Anti-Rationalizations

| You might think... | But actually... |
|--------------------|-----------------|
| "CI is probably green" | Check it. "Probably" is not evidence. |
| "This doesn't need a PR, I'll push directly" | PRs are the audit trail. Create one. |
| "The checklist is overkill for this change" | The checklist exists because someone shipped without it and broke prod. |
| "I'll clean up the worktrees later" | Disk fills up. Clean now. |
| "Staging looked fine, no need to check prod" | Staging != production. Verify both. |

## Gotchas

- Merged without running tests on the merge result (tests passed on branch, failed on merge)
- Created PR but forgot to push the branch first
- Deployed without environment variables configured (got 401/500 in production)
- Forgot to clean up worktrees — disk space accumulated
