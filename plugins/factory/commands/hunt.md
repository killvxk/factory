---
description: "Investigate and fix a bug from root cause to verified resolution."
---

# /factory:hunt

## Preflight

- Verify the task is a bug, regression, or unexpected behavior.
- Verify the working directory with `pwd`.
- Check `docs/solutions/` and `.factory/learnings.jsonl` for prior art before changing code.

## Plan

- Trace the execution path from symptom to root cause before proposing a fix.
- Use `spawn_agent` for focused reproduction or evidence gathering when the failure surface is broad.
- State one testable hypothesis at a time and reject it with evidence if it is wrong.

## Commands

- Inspect the failing path, the relevant skill or command wrapper, and the surrounding config before editing.
- Change only the minimum code needed to fix the verified root cause.
- Add or update a regression test when the bug is in executable code.

## Verification

- The output should name the root cause, the fix, and the verification command that proves the symptom is gone.
- Do not claim success until the original failure is reproduced as resolved.

## Summary

- Result: root cause identified and fixed with evidence.
- Status: success only when the bug is verified gone.

## Next Steps

- Move to `/factory:check` for the repaired diff, or back to `/factory:build` if more implementation is still needed.
