---
name: factory-full-dev
description: "Runs the full Factory workflow end to end across design validation, planning, implementation, review, ship readiness, and knowledge capture. Use when the user wants one coordinated workflow for a feature or substantial change. Triggers: full dev, full workflow, end to end, run Factory, handle this feature properly."
---

# Factory Full-Dev Skill - End-to-End Workflow

## Goal

Coordinate the full Factory workflow from framing through verification.
Use the factory-think, factory-plan, factory-build, factory-check, factory-ship, and factory-compound skills in order, with explicit gates between phases.

## Entry Rules

- Use this skill when the user wants one coordinated feature workflow, not just a single phase.
- If the user only needs debugging, review, or planning, route to factory-hunt, factory-check, or factory-plan directly.
- Never skip the think or plan phases for non-trivial work.

## Workflow

1. **Think** - Run the factory-think skill first. No code until the problem is framed, trade-offs are compared, and the user approves a direction.
2. **Plan** - Run the factory-plan skill next. Produce a task list with file paths, acceptance criteria, and verification commands. Get user approval before implementation.
3. **Build** - Execute the approved plan through the factory-build skill. For parallelizable work, use subagents with clear ownership and keep the main session coordinating.
4. **Check** - Review the merged result through the factory-check skill. Findings come before summaries. Do not ship with unresolved P0 findings.
5. **Ship** - Once build, tests, and review evidence are clean, run the factory-ship skill to decide how the work should land.
6. **Compound** - Capture durable learnings in `docs/solutions/` and `.factory/learnings.jsonl` through the factory-compound skill.

## Human Gates

- **G1**: User approves the design direction after factory-think.
- **G2**: User approves the implementation plan after factory-plan.
- **G3**: Build, tests, and review evidence are clean before ship.

## Coordination Rules

- The coordinator does not write core feature code unless needed to unblock immediately.
- Implementation agents own disjoint files or modules.
- Review happens on the integrated result, not fragmented branches.
- Quality fixes must re-run relevant verification after each round.

## Exit Criteria

Do not claim completion unless all of the following are true:

- The approved scope is implemented.
- Relevant build, typecheck, lint, and test steps have run or their gaps are explicitly reported.
- Review findings are triaged and no P0 findings remain.
- Knowledge capture is complete for non-trivial work.

## Verification

Before closing, verify that:

- The current phase gate is satisfied before moving forward.
- Final build and test evidence is fresh.
- The final response includes remaining risks or verification gaps, if any.
