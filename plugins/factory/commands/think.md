---
description: "Validate a proposed direction before any code is written."
---

# /factory:think

## Preflight

- Verify the request is a real design question, feature start, or architecture decision.
- Verify the working directory with `pwd`.
- Check `docs/solutions/` and `.factory/learnings.jsonl` for prior art before proposing anything.

## Plan

- Identify the actual problem, boundaries, and success criteria.
- Use `spawn_agent` for a read-only research pass if the space is broad or unfamiliar.
- Ask 1-3 high-value questions first, then propose options only after the scope is clear.
- Attack the recommendation before presenting it.

## Commands

- Inspect plugin-local solution history in `docs/solutions/`.
- Inspect prior learnings in `.factory/learnings.jsonl`.
- If needed, spawn a research agent to summarize relevant prior work.

## Verification

- The output should include the chosen direction, trade-offs, key constraints, and open questions.
- No implementation details should be proposed before user approval.

## Summary

- Result: design direction validated or clarified.
- Status: success only when the user can choose a direction.

## Next Steps

- Move to `/factory:plan` once the direction is approved.

