# Verification Checklists

Every skill must satisfy its verification checklist before claiming completion.
"Seems right" is not a completion criterion. Evidence is.

Source: agent-skills (Addy Osmani), superpowers (Jesse Vincent).

## Universal Checklist (applies to ALL skills)

- [ ] All modified files saved and confirmed correct
- [ ] No TODO/TBD/FIXME introduced without explicit approval
- [ ] No hardcoded secrets, paths, or environment-specific values
- [ ] Verification command executed with exit code 0
- [ ] Output of verification command reviewed (not just exit code)

## Per-Skill Checklists

### /think
- [ ] Interactive exploration completed (questions asked, answers heard, assumptions surfaced)
- [ ] Spec review passed (completeness, consistency, feasibility, clarity) for Standard/Deep
- [ ] At least 2 approaches proposed with trade-offs
- [ ] Own recommendation attacked before presenting
- [ ] docs/solutions/ consulted for prior art
- [ ] Multi-perspective validation completed (user/engineering/security) for Standard/Deep depth
- [ ] No code, pseudo-code, or scaffolding in output

### /plan
- [ ] Every task has exact file paths and verification commands
- [ ] Parallel tasks explicitly marked with no blocking dependencies
- [ ] Each task has acceptance criteria
- [ ] Plan spec review passed (completeness, ordering, isolation, verifiability)
- [ ] Deep plan review dispatched for 10+ task plans

### /build
- [ ] Every feature has a failing test written BEFORE implementation
- [ ] All tests pass after implementation
- [ ] Spec compliance verified BEFORE code quality review
- [ ] Build command succeeds with zero warnings

### /hunt
- [ ] Root cause stated in one sentence (file, function, line, condition)
- [ ] Fix verified against the original reproduction steps
- [ ] Regression test added
- [ ] No more than 5 files touched for a single bug

### /check
- [ ] All review personas ran to completion
- [ ] Findings below 0.60 confidence suppressed
- [ ] P0 findings: zero remaining
- [ ] Verification script executed (not skipped)

### /compound
- [ ] Document saved to docs/solutions/ with YAML frontmatter
- [ ] Learning logged to .factory/learnings.jsonl
- [ ] Problem, solution, and key decisions all documented

### /design
- [ ] Direction locked via 4 mandatory questions
- [ ] Opened in browser and visually verified
- [ ] Not a template (would swapping content require layout changes?)
- [ ] No banned fonts (Inter, Roboto, system-ui as display)

### /ship
- [ ] All tests pass on merged code
- [ ] CI checked and confirmed passing (not assumed)
- [ ] Pre-launch checklist completed (6 categories)
- [ ] One of 4 structured options selected (merge/PR/keep/discard)

### /health
- [ ] Six-layer audit complete
- [ ] Dual-agent audit dispatched for Standard/Complex tiers (or local-only for Simple)
- [ ] Severity classified ([!] critical, [~] structural, [-] incremental)
- [ ] No PASS items fabricated without evidence

### /retro
- [ ] Week's work reviewed with evidence (git log, not memory)
- [ ] .factory/learnings.jsonl reviewed for past week's entries
- [ ] .factory/learnings.jsonl pruned if >200 lines
- [ ] docs/solutions/ freshness checked (Keep/Update/Delete each doc)
- [ ] At least 1 actionable improvement identified
