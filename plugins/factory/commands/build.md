---
description: "Implement an approved plan with strict TDD and isolated worktrees."
---

# /factory:build

## Preflight

- Verify an approved plan exists.
- Verify the working tree is clean enough to branch from.
- Verify the tasks are split so that parallel worktrees will not overlap on the same files.

## Plan

- Create a pre-development checkpoint.
- Keep the main session as coordinator only.
- Use `spawn_agent` to launch isolated subagents in their own git worktrees.
- Enforce the red-green-refactor cycle for every change.

## Commands

- Create a checkpoint commit before spawning workstreams.
- Spawn one subagent per independent task in a dedicated worktree.
- Require each subagent to report branch, worktree, tests, and blockers.
- Merge completed branches in dependency order and verify the merged result.

## Verification

- Every production change must be preceded by a failing test.
- The merged result must pass the relevant build and test commands.
- The review handoff must wait until integration is complete.

## Summary

- Result: approved plan implemented.
- Status: success only when build and tests pass on the merged tree.

## Next Steps

- Move to `/factory:check` after integration, then `/factory:ship` and `/factory:compound`.

