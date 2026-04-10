---
description: "Audit the Codex plugin setup and local configuration surfaces."
---

# /factory:health

## Preflight

- Verify the task is about setup, discovery, or configuration health.
- Verify the current directory with `pwd`.

## Plan

- Audit the plugin manifest, marketplace entry, skill discovery, command presence, and Codex config surfaces.
- Prefer local evidence over assumptions.

## Commands

- Inspect the repo-local plugin structure.
- Check that the exposed commands and skills match the plugin contents.

## Verification

- Report any missing, stale, or inconsistent Codex surfaces.

## Summary

- Result: setup health assessed.
- Status: success only when the audit is evidence-based.

## Next Steps

- Fix the specific configuration issue or continue with the normal workflow.

