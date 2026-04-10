---
title: "Distilling 5 plugins into 1 self-contained Factory plugin"
date: 2026-04-09
problem_type: architecture
components: [factory, skills, agents, references, pipeline]
tags: [plugin-design, architecture, distillation, compound-engineering, agent-skills, waza, superpowers, gstack, legacy-claude]
---

## Problem

5 existing Claude Code plugins (compound-engineering, agent-skills, Waza, superpowers, gstack) each had unique strengths but also weaknesses. A hand-stitched `full-development.md` command chained external skills from superpowers and compound-engineering, creating fragile external dependencies.

## Root Cause

No single plugin covered the full engineering lifecycle with both quality enforcement and knowledge persistence. Each plugin optimized for its own philosophy, leaving gaps that others filled.

## Solution

Created a new self-contained plugin with 10 skills, 4 agents, 3 shared references, and 1 pipeline command. Each skill internalizes the best from all 5 sources using a specific "DNA recipe" mapping.

Legacy context: this document records the Claude Code-era distillation that produced the prompt assets now being reused in Codex. The migration preserves the assets, but the runtime packaging changes to plugin-local Codex semantics.

Key design decisions:
- **Waza writing style**: goals + constraints + gotchas, not step-by-step (future-proof as models improve)
- **Superpowers enforcement**: Iron Laws + anti-rationalization tables in every skill
- **Compound memory**: docs/solutions/ + .factory/learnings.jsonl knowledge loop
- **Agent Skills organization**: lifecycle phases + verification checklists
- **gstack persistence**: JSONL learnings + `docs/memory/CODEX.local.md` for retro

## Key Decision

Fork the structure from Waza (8 skills, minimal framing) rather than Compound (41 skills) or gstack (41 skills). Reason: adding 2 skills to a focused 8 is easier than cutting 31 from a bloated 41. Minimal framing also compounds as models improve.

## Prevention

- Self-review audit after every major change (DNA recipe compliance matrix)
- Anti-rationalization tables prevent skill drift toward bloat
- /retro prunes stale knowledge quarterly
