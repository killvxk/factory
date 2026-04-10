---
description: "Review completed changes with parallel specialist agents and evidence-based verification."
---

# /factory:check

## Preflight

- Verify the change set is complete enough for review.
- Verify the scope is not still in the design phase.
- Gather the files changed since the last checkpoint.

## Plan

- Classify review depth from the size and risk of the diff.
- Use `spawn_agent` to run correctness, security, and adversarial reviewers in parallel when warranted.
- Keep confidence thresholds explicit and block on P0 findings.

## Commands

- Dispatch reviewer agents in parallel.
- Summarize findings by severity, not by reviewer personality.
- Run the project’s relevant verification command after fixes.

## Verification

- P0 findings must be reduced to zero before the review passes.
- Evidence from build or test output must be included when the review is used for release gating.

## Summary

- Result: reviewed and either approved or blocked with findings.
- Status: success only when the review passes with evidence.

## Next Steps

- Fix findings or move to `/factory:ship` if the review passes.

