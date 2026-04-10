---
name: factory-think
description: "Validates design before any code is written. Use when starting a new feature, facing an architecture decision, or when the user says think, design this, plan this, let's discuss, brainstorm, or before building anything new. NOT for bug fixes (use the factory-hunt skill) or code review (use the factory-check skill)."
---

# Factory Think Skill - Design and Validate Before You Build

## Goal

Ensure no code is written until the problem is understood, approaches are compared,
and the user has approved a direction. Consult plugin-local solutions to avoid repeating mistakes.

## Constraints

- ZERO code, pseudo-code, scaffolding, or implementation until user approves
- Always check `docs/solutions/` and `.factory/learnings.jsonl` for prior art before proposing anything
- Always attack your own recommendation before presenting it
- If the user says "just do it" or "help me build it": this means EXECUTE, not "present options"

## On Entry

1. Check `docs/solutions/` and `.factory/learnings.jsonl` for relevant prior art.
2. If the request needs a deeper search, spawn a read-only research agent with `spawn_agent`.
3. Verify current directory with `pwd`. Never assume paths.
4. If the request involves external services, list ALL dependencies before starting.

## Interactive Exploration (Brainstorm Phase)

Before proposing ANY solution, explore the problem space WITH the user.
Do NOT skip this phase. Do NOT jump to solutions.

### Depth Assessment

Classify first to calibrate how much exploration is needed:
- **Lightweight** (single-file change, well-understood domain): 1-2 questions, then propose
- **Standard** (multi-file, some unknowns): 3-5 questions across 2 rounds
- **Deep** (new system, external contracts, security-sensitive): full Socratic exploration

### Socratic Exploration

Ask questions that expose assumptions, not just gather requirements.
The goal is to discover what the user HASN'T thought about yet.

Question categories (pick the most relevant, don't ask all):
- **Boundary**: "What's explicitly OUT of scope?" "Where does this feature end?"
- **User scenario**: "Walk me through how a user would actually use this."
- **Failure mode**: "What happens if this fails? What's the blast radius?"
- **Constraint**: "What technical/business constraints should I know about?"
- **Prior art**: "Has anything similar been attempted before? What happened?"
- **Priority**: "If you could only ship ONE aspect of this, which one?"

### Exploration Rules

- Ask 1-3 questions at a time, not a wall of 10 questions
- Listen to the answer. Follow up on surprising or vague responses.
- Surface your own assumptions explicitly: "I'm assuming X - is that right?"
- If the user says "you decide" on a design choice, propose 2 options with trade-offs
  and pick one with a stated reason. Don't punt back.
- When enough is understood (you can describe the solution to a third person),
  transition to proposing approaches.

### Scope Modes

When the user's intent about scope is ambiguous, ask which posture to take:
- **expand**: explore adjacent possibilities
- **shape**: refine the current direction
- **hold**: validate without changing scope
- **cut**: simplify, remove, reduce

## Propose Approaches

Only after Interactive Exploration has built shared understanding.

For each approach:
- Name it concisely
- State the trade-off (what you gain, what you lose)
- Include one MINIMAL option (least change, least risk)
- Attack your own recommendation: "This could fail if..."

## Multi-Perspective Validation (from gstack)

For Standard and Deep depth, evaluate the proposal from three lenses before presenting:

1. **User lens**: Does this solve a real user pain? Would they notice the difference?
2. **Engineering lens**: Is the architecture sound? Does it handle 10x growth? What breaks first?
3. **Security lens**: What can be exploited? Where are the trust boundaries?

State one concern from each lens. If any concern is unresolved, investigate before presenting.
Do NOT create separate review steps for each lens - integrate them into your proposal naturally.

## Spec Review (before presenting to user)

After formulating the design but BEFORE presenting, review your own output:

### Self-Review Checklist (all depths)

- [ ] Addresses the user's actual problem (not a reframed version you prefer)
- [ ] All assumptions surfaced during exploration are resolved or explicitly listed
- [ ] No contradictions between proposed approach and stated constraints
- [ ] Scope matches what was agreed during exploration (no stealth additions)

### Structured Spec Review (Standard/Deep only)

For Standard and Deep depth, review from 4 dimensions:

1. **Completeness**: Does the design cover all requirements discovered in exploration?
   If anything is missing, add it or explain why it's deferred.
2. **Consistency**: Do the parts fit together? Any contradictions between components?
3. **Feasibility**: Can this actually be built with the stated constraints?
   Check: dependencies exist, APIs available, timeline realistic.
4. **Clarity**: Could a different engineer (or the plan skill) take this and produce tasks
   without asking clarifying questions? If not, add detail.

If any dimension fails, fix the design before presenting. Do NOT present a spec
you know has gaps.

## Confidence Check

After spec review, self-score on 3 axes (1-5):
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

## Verification

Before exiting, consult `references/verification-checklists.md` for the think checklist.

## Gotchas

These are from real project failures, not theoretical risks:

- Wrong path assumed (`~/project` vs `~/www/project`) - always run `pwd` first
- Credentials or API keys surfaced mid-build - list ALL dependencies upfront
- Designed around an MCP tool that wasn't loaded - verify tool availability
- Rejected design restarted from scratch instead of narrowing - ask WHAT failed and iterate
- Regional API variants assumed identical (e.g., Shengwang vs Agora) - verify endpoints
- Added a new runtime (e.g., FastAPI into a Next.js project) without user approval

## Anti-Rationalizations

| You might think... | But actually... |
|--------------------|-----------------|
| "Let's just start coding and figure it out" | That's how you build the wrong thing. The think skill exists to prevent this. |
| "The requirements are clear enough" | List 3 ambiguities. If you can't, you haven't looked hard enough. |
| "This is a small change, no need to think" | Small changes to the wrong thing are still wrong. |
| "We can refactor later" | Refactoring debt compounds. Design it right. |
| "I already know the best approach" | Then it should take 2 minutes to document why. Do it. |
| "The user already told me what they want" | They told you WHAT. You need to discover WHY, boundaries, and failure modes. |
| "Asking questions slows us down" | Building the wrong thing slows you down more. 3 questions now saves 3 hours later. |
