# Factory Plugin - Design Spec

## One-Line Definition

10 engineering habits x anti-rationalization x compounding knowledge.

## Origin

Distilled from 5 projects (2026-04-07 analysis session):

| Source | Version | What We Took |
|--------|---------|-------------|
| compound-engineering | 2.63.0 | Knowledge compounding loop, multi-persona review, confidence gating, composable modes |
| agent-skills (Addy Osmani) | 1.0.0 | Anti-rationalization tables, lifecycle phases, verification checklists, Google SWE culture |
| Waza (tw93) | 3.1.0 | Minimal framing philosophy, evidence-based gotchas, 8-skill structure, trust-the-model writing |
| superpowers (Jesse Vincent) | 5.0.7 | Iron Laws, persuasion science for compliance, CSO, two-stage review, TDD-for-skills |
| gstack (Garry Tan) | 0.15.15.1 | Persistent JSONL learning, 3-tier skill eval, preamble tiers, sprint pipeline |

Legacy predecessor: `~/.claude/commands/full-development.md` (433-line hand-stitched pipeline calling external skills). Kept here as migration history; Codex execution now uses plugin-local commands, references, and subagents.

## Architecture

```text
repo-root/
|-- docs/solutions/     Root knowledge store for durable writeups
|-- docs/memory/        Latest Codex local notes
|-- .factory/           Shared JSONL learnings
|-- .agents/plugins/    Local marketplace registry
`-- plugins/factory/
    |-- .codex-plugin/   Codex manifest
    |-- skills/         10 self-contained plugin-local SKILL.md directories
    |-- agents/         4 local reviewer/research prompt assets loaded by Codex workflows
    |-- references/     Shared anti-rationalizations, iron laws, verification checklists
    |-- commands/       Pipeline command wrappers
    `-- docs/           Plugin-local design and runtime notes
```

## Writing Philosophy

1. Waza style: state goals + constraints, not step-by-step procedures. Let models grow.
2. Every skill has an anti-rationalization table (Superpowers + Agent Skills).
3. Every skill has a verification exit checklist (Agent Skills).
4. Description field: ONLY triggering conditions, never workflow summaries (Superpowers CSO).
5. Evidence-based gotchas: only from real failures, not theoretical risks (Waza).

## 10 Skills

| # | Skill | Replaces | Core DNA |
|---|-------|----------|----------|
| 1 | think | superpowers:brainstorming | Waza structure + Compound learnings lookup |
| 2 | plan | ce:plan | Compound planning + Agent Skills lifecycle |
| 3 | build | superpowers:TDD + parallel subagents | Superpowers Iron Law + Codex subagents + two-stage review |
| 4 | hunt | (new) | Waza structure + Superpowers systematic-debugging |
| 5 | check | ce:review + review-fix | Waza structure + Compound 3-persona + confidence gating |
| 6 | compound | ce:compound | Compound knowledge + gstack JSONL learnings |
| 7 | design | (new) | Waza anti-template + gstack screenshot verify |
| 8 | ship | (new) | gstack ship + Agent Skills pre-launch + Superpowers 4-option |
| 9 | health | (new) | Waza dual-agent audit + gstack weighted scoring |
| 10 | retro | (new) | gstack retro + Compound compound-refresh |

## 4 Agents

The four reviewer/research personas live as plugin-local prompt assets. Codex workflows load them on demand; they are not Claude-registered agents.

| Agent | From | Purpose |
|-------|------|---------|
| correctness.md | Compound | Logic errors, edge cases, state bugs |
| security.md | Compound | Auth, injection, secrets, trust boundaries |
| adversarial.md | Compound | Construct failure scenarios, stress-test |
| learnings-researcher.md | Compound | Search docs/solutions/ before think/check |

## 3 Iron Laws (from Superpowers)

1. NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST
2. NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST
3. NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE

## Pipeline (commands/full-dev.md)

```text
/factory:think -> /factory:plan -> /factory:build (Codex subagent parallel TDD) -> /factory:check -> /factory:ship -> /factory:compound
```

3 human gates (vs AutoNeeds' 6, vs full-development.md's 5):
- G1: After /factory:think - confirm direction
- G2: After /factory:plan - confirm task list
- G3: After /factory:check - confirm ready to ship

## Future Extension Point

`pipeline/` directory for AutoNeeds market-research + debate system.
