---
name: check
description: "Use after completing any implementation task, before merging. Triggers: check, review, review my code, look at this, is this ready, before merge. NOT for debugging (use /hunt) or design (use /think)."
version: 1.0.0
---

# /check — Multi-Persona Review Before You Ship

## Goal

Review code changes through parallel specialist agents, auto-fix safe issues,
block on critical findings, and require evidence-based verification before passing.

## Constraints

- Never skip review for "trivial" changes
- Suppress findings below 0.60 confidence (reduces noise without hiding real issues)
- P0 findings must reach zero before passing
- Composable: supports interactive, autofix, and headless modes

## Depth Classification

| Depth | Criteria | Reviewers |
|-------|----------|-----------|
| Quick | <100 lines, 1-5 files | correctness only |
| Standard | 100-500 lines, 6-10 files | correctness + security + conditional specialists |
| Deep | 500+ lines, 10+ files, or auth/payments/data | all personas + adversarial pass |

Override: $CLAUDE_EFFORT = max → force Deep regardless of line count; $CLAUDE_EFFORT = low → cap at Quick.

## Review Personas

Dispatch in parallel via the Agent tool. Each returns structured findings.

1. **`factory:correctness`** (always-on) — Logic errors, edge cases, state bugs,
   error propagation, null/undefined paths. Confidence threshold: 0.60.

2. **`factory:security`** (conditional: auth, user input, public endpoints) —
   Injection, auth bypass, secrets in code/logs, SSRF, path traversal.
   Lower threshold: 0.50 for P0 (missed vulns are costlier than false positives).

3. **`factory:adversarial`** (conditional: Deep reviews or high-risk domains) —
   "If I were trying to break this through this diff, what would I exploit?"
   Four angles: assumption violation, composition failures, cascade construction, abuse cases.

Deep review dispatch order (Deep depth only):
1. correctness + security: dispatched in PARALLEL
2. adversarial: dispatched AFTER correctness and security complete

## Autofix Classification

Every finding must declare one:
- **safe_auto**: apply immediately (formatting, unused imports, simple type fixes)
- **gated_auto**: batch and confirm with user (logic changes, API adjustments)
- **manual**: requires human judgment (architecture, design decisions)
- **advisory**: noted in report, no action needed

## Modes

- **interactive** (default): present findings, ask user for decisions on gated_auto + manual
- **autofix**: apply safe_auto silently, create tasks for remainder, never ask user
- **headless**: return structured JSON for pipeline consumption (used by /build and full-dev)
  headless mode MUST immediately write all findings to `.omc/check-findings-{timestamp}.json` before proceeding to verification. This ensures findings survive context compaction.

### PR Comment Mode
When invoked with `--pr-comment` and a PR exists on the current branch:
1. Detect PR: `gh pr view --json number -q .number` — if no PR found, skip PR comments and output local report only
2. For each P0-P2 finding with File:{path}:{line}, post inline comment via gh api
3. P3/advisory: include in summary comment only, not inline
4. After safe_auto fixes: delete corresponding inline comments

## Verification

After all fixes applied:
1. Auto-detect project type and run appropriate checks:
   - `Cargo.toml` -> `cargo check && cargo test`
   - `tsconfig.json` -> `npx tsc --noEmit && npm test`
   - `package.json` -> `npm test`
   - `pytest.ini` / `pyproject.toml` -> `pytest`
   - `Makefile` -> `make test`
2. Paste last 10 lines of output as evidence.
3. If any verification fails: fix before reporting pass.

## Hard Stops (non-negotiable blocks)

- Destructive commands without confirmation (rm -rf, DROP TABLE, --force, --no-verify)
- SQL/command/path injection in user-facing code
- Hardcoded secrets or credentials
- References to identifiers that don't exist in the codebase (grep first)
- Release artifacts missing when release is claimed

## Soft Signals (flag, don't block)

- Side effects in pure functions
- Magic numbers/literals
- Dead code
- Untested code paths
- N+1 query patterns

## Verification

Before closing, consult `references/verification-checklists.md` for the /check checklist.

## Gotchas

- Commented on wrong GitHub issue (#249 vs #255) — always `gh issue view N` to confirm
- PR comments sounded like a formal report — keep it 1-2 sentences, natural
- Announced release done before uploading artifacts — verify artifacts exist
- Skipped verification on "trivial" change — trivial changes break production
- Deployed without env vars configured — check deployment config
- Git push failed from auth mismatch (HTTPS vs SSH) — verify remote URL

> Anti-rationalizations for this skill: see `references/anti-rationalizations.md` § Check Phase and Universal Rationalizations.
