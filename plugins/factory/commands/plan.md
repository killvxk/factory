---
description: "Turn an approved direction into a zero-ambiguity implementation plan."
---

# /factory:plan

## Preflight

- Verify a direction has already been approved by `/factory:think`.
- Verify the working directory with `pwd`.
- Check `docs/solutions/` and `.factory/learnings.jsonl` for relevant prior implementations.

## Plan

- Convert the approved direction into ordered tasks with exact file paths, acceptance criteria, and verification commands.
- Mark which tasks can run in parallel and which are blocked.
- If the plan is deep, use `spawn_agent` for a read-only review of scope and ordering.

## Commands

- Read the approved direction from the current conversation context.
- Cross-check with plugin-local solutions and learnings.
- Produce a task list that another engineer can execute without asking questions.

## Verification

- Every task must include files, acceptance criteria, and a concrete verify command.
- No task may contain placeholders such as `TBD` or `TODO`.

## Summary

- Result: implementation plan ready for execution.
- Status: success only when the plan is decision-complete.

## Next Steps

- Move to `/factory:build` when the plan is approved.

