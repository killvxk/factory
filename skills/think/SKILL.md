---
name: think
description: "Validates design before any code is written. Use when starting a new feature, facing an architecture decision, or when the user says think, design this, plan this, let's discuss, brainstorm, or before building anything new. NOT for bug fixes (use /hunt) or code review (use /check)."
version: 1.0.0
---

# /think — Design and Validate Before You Build

## Goal

Ensure no code is written until the problem is understood, approaches are compared,
and the user has approved a direction. Consult past solutions to avoid repeating mistakes.

## Constraints

- ZERO code, pseudo-code, scaffolding, or implementation until user approves
- Always check `docs/solutions/` for prior art before proposing anything
- Always attack your own recommendation before presenting it
- If the user says "just do it" or "help me build it": this means EXECUTE, not "present options"

## On Entry

1. Check `docs/solutions/` — dispatch the `factory:learnings-researcher` agent to find
   relevant past solutions. Surface the top 3 if any exist.
2. Verify current directory with `pwd`. Never assume paths.
3. If the request involves external services, list ALL dependencies before starting.

## Approach

Assess depth first:
- **Lightweight** (single-file change, well-understood domain): 1 round, 2 options
- **Standard** (multi-file, some unknowns): 2 rounds, 2-3 options with trade-offs
- **Deep** (new system, external contracts, security-sensitive): full validation cycle

For each approach proposed:
- Name it concisely
- State the trade-off (what you gain, what you lose)
- Include one MINIMAL option (least change, least risk)
- Attack your own recommendation: "This could fail if..."

## Scope Modes

When the user's intent is ambiguous, ask which posture to take:
- **expand**: explore adjacent possibilities
- **shape**: refine the current direction
- **hold**: validate without changing scope
- **cut**: simplify, remove, reduce

## Confidence Check

Before presenting to user, self-score on 3 axes (1-5):
- Requirements clarity
- Technical feasibility
- Risk awareness

If any axis scores below 3, loop back and investigate that axis before presenting.

## Exit

User explicitly approves a direction. Output a brief summary of:
- Chosen approach (1-2 sentences)
- Key constraints identified
- Files/modules that will be affected
- Open questions (if any)

## Gotchas

These are from real project failures, not theoretical risks:

- Wrong path assumed (`~/project` vs `~/www/project`) — always run `pwd` first
- Credentials or API keys surfaced mid-build — list ALL dependencies upfront
- Designed around an MCP tool that wasn't loaded — verify tool availability
- Rejected design restarted from scratch instead of narrowing — ask WHAT failed and iterate
- Regional API variants assumed identical (e.g., Shengwang vs Agora) — verify endpoints
- Added a new runtime (e.g., FastAPI into a Next.js project) without user approval

## Anti-Rationalizations

| You might think... | But actually... |
|--------------------|-----------------|
| "Let's just start coding and figure it out" | That's how you build the wrong thing. /think exists to prevent this. |
| "The requirements are clear enough" | List 3 ambiguities. If you can't, you haven't looked hard enough. |
| "This is a small change, no need to think" | Small changes to the wrong thing are still wrong. |
| "We can refactor later" | Refactoring debt compounds. Design it right. |
| "I already know the best approach" | Then it should take 2 minutes to document why. Do it. |
