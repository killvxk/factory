---
name: compound
description: "Captures solved problems as searchable knowledge for the Factory Codex plugin. Use after solving a non-trivial problem, completing a feature, or learning something the team should know. Triggers: compound, save this, document what we learned, knowledge capture, what did we learn. NOT for planning (use /factory:plan) or review (use /factory:check)."
---

# /factory:compound - Make Every Solution Count Twice

## Goal

Document solved problems so future `/factory:think`, `/factory:check`, and `/factory:retro` sessions can find them.
Use two storage layers: structured docs for deep solutions, JSONL for quick learnings.

## Constraints

- Write while context is fresh - don't defer to "later"
- `docs/solutions/` for problems worth a full write-up: design decisions, architecture, tricky bugs
- `.factory/learnings.jsonl` for quick operational notes: gotchas, config tips, tool quirks
- Every doc must have YAML frontmatter for searchability

## Deep Storage: docs/solutions/

Create a markdown file with YAML frontmatter:

```yaml
---
title: "Brief description of the problem and solution"
date: YYYY-MM-DD
problem_type: bug | architecture | performance | integration | process
components: [list, of, affected, modules]
tags: [searchable, keywords]
---
```

Body structure:
1. **Problem**: What went wrong and how it manifested
2. **Root Cause**: Why it happened (1-2 sentences)
3. **Solution**: What was done to fix it
4. **Key Decision**: Why THIS solution over alternatives
5. **Prevention**: How to prevent recurrence (test, lint rule, design pattern)

Keep it concise. A good solution doc is 20-50 lines, not a novel.

## Quick Storage: .factory/learnings.jsonl

Initialize if needed, then append one JSON line per learning. Schema per line:
```json
{"date":"YYYY-MM-DD","skill":"think|plan|build|hunt|check|compound|design|ship|health|retro","learning":"one sentence","tags":["searchable","keywords"]}
```

Future `/factory:think` and `/factory:check` sessions should surface the top 3 most relevant entries by launching the plugin-local research prompt asset through `spawn_agent`.

## Project Memory

- `docs/memory/CODEX.local.md` is the project-local memory surface for the latest retro and active notes.
- Keep the current state short and current there; keep durable explanations in `docs/solutions/` and `.factory/learnings.jsonl`.
- Use the memory file for what Codex should pick up next session, not for full solution write-ups.

## Repository Policy

`.factory/` should be committed to the repo as shared team knowledge.

## When to Use Which

| Situation | Storage |
|-----------|---------|
| Tricky bug with non-obvious root cause | docs/solutions/ |
| Architecture decision with trade-offs | docs/solutions/ |
| Quick operational tip | .factory/learnings.jsonl |
| Config gotcha | .factory/learnings.jsonl |
| Tool-specific workaround | .factory/learnings.jsonl |

## Integration with Other Skills

- `/factory:think` launches the plugin-local research prompt asset through `spawn_agent` to search `docs/solutions/` and `.factory/learnings.jsonl` before proposing
- `/factory:check` uses the same plugin-local research prompt asset when reviewing code that touches previously documented areas
- `/factory:retro` reviews and cleans up stale `docs/solutions/` entries, then refreshes `docs/memory/CODEX.local.md`

## Auto-Surface Mechanism

The `/factory:compound` skill writes knowledge. Surfacing stays pull-based:
1. `/factory:think` launches the plugin-local research prompt asset through `spawn_agent`, which searches both stores before proposing
2. `/factory:check` launches the same plugin-local prompt asset when reviewing code that touches previously documented areas
3. No session-start hook is needed; skills pull prior art only when they need it

## Verification

Before closing, verify the new knowledge entry is searchable and the solution doc has valid frontmatter, concise root cause, and an explicit prevention note.

## Anti-Rationalizations

| You might think... | But actually... |
|--------------------|-----------------|
| "I'll document it later" | Context decays in hours. Write it now. |
| "The code is self-documenting" | Code says WHAT, not WHY. Document the decision. |
| "This is too small to document" | Small gotchas compound. 1 line in JSONL takes 10 seconds. |
| "Nobody will read this" | Future /think will auto-surface it. The reader is the machine. |
| "The PR description covers it" | PR descriptions are on GitHub. `docs/solutions/` is in the repo. |

## Gotchas

- Wrote a solution doc so long nobody would read it — keep it 20-50 lines
- Forgot YAML frontmatter — doc becomes unsearchable
- Documented the fix but not the WHY — future reader can't judge if it still applies
- Deferred writing to "after the PR" — context was lost, doc was never written
