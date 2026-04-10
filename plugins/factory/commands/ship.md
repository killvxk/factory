---
description: "Finish the delivery path: merge, PR, or deploy with verification."
---

# /factory:ship

## Preflight

- Verify implementation and review are complete.
- Verify build and tests are green.
- Verify the user wants delivery work rather than more changes.

## Plan

- Present the available delivery paths: merge, PR, keep, or discard.
- Prefer the least risky path that satisfies the request.

## Commands

- Check the current branch state.
- If asked to merge or open a PR, perform the delivery action and record the outcome.

## Verification

- Delivery is valid only when the requested action is completed and verifiable.

## Summary

- Result: delivery path completed or prepared.
- Status: success only when the chosen path is executed.

## Next Steps

- Use `/factory:compound` to capture the result as knowledge.

