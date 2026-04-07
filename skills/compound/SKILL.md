---
name: compound
description: "Captures solved problems as searchable knowledge. Use after solving a non-trivial problem, completing a feature, or learning something the team should know. Triggers: compound, save this, document what we learned, knowledge capture, what did we learn. NOT for planning (use /plan) or review (use /check)."
version: 1.0.0
---

# /compound — Make Every Solution Count Twice

## Goal

Document solved problems so future /think and /check sessions can find them.
Two storage layers: structured docs for deep solutions, JSONL for quick learnings.

## Constraints

- Write while context is fresh — don't defer to "later"
- docs/solutions/ for problems worth a full write-up (design decisions, architecture, tricky bugs)
- .factory/learnings.jsonl for quick operational notes (gotchas, config tips, tool quirks)
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

Append one JSON line per learning:

```json
{"date":"2026-04-07","skill":"hunt","learning":"MCP tool failures: diagnose before switching tools","tags":["mcp","debugging"]}
```

Future sessions surface the top 3 most relevant learnings via tag matching.

## When to Use Which

| Situation | Storage |
|-----------|---------|
| Tricky bug with non-obvious root cause | docs/solutions/ |
| Architecture decision with trade-offs | docs/solutions/ |
| Quick operational tip | .factory/learnings.jsonl |
| Config gotcha | .factory/learnings.jsonl |
| Tool-specific workaround | .factory/learnings.jsonl |

## Integration with Other Skills

- `/think` dispatches `factory:learnings-researcher` to search docs/solutions/ before proposing
- `/check` references relevant past solutions when reviewing similar code
- `/retro` reviews and cleans up stale docs/solutions/ entries

## Gotchas

- Wrote a solution doc so long nobody would read it — keep it 20-50 lines
- Forgot YAML frontmatter — doc becomes unsearchable
- Documented the fix but not the WHY — future reader can't judge if it still applies
- Deferred writing to "after the PR" — context was lost, doc was never written
