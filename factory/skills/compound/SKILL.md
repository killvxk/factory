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

Initialize if needed, then append one JSON line per learning:

```bash
mkdir -p .factory
echo '{"date":"'"$(date +%Y-%m-%d)"'","skill":"hunt","learning":"MCP tool failures: diagnose before switching tools","tags":["mcp","debugging"]}' >> .factory/learnings.jsonl
```

Schema per line:
```json
{"date":"YYYY-MM-DD","skill":"think|plan|build|hunt|check|compound|design|ship|health|retro","learning":"one sentence","tags":["searchable","keywords"]}
```

Future `/think` and `/check` sessions auto-surface the top 3 most relevant entries via `factory:learnings-researcher`.

## .gitignore Decision

`.factory/` should be **committed to the repo** (team knowledge, not personal state).
Add to `.gitignore` ONLY if the team decides learnings are personal:
```
# Uncomment if learnings should be personal, not shared:
# .factory/learnings.jsonl
```

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

## Auto-Surface Mechanism

The `/compound` skill writes knowledge. The surfacing happens via:
1. `/think` on entry dispatches `factory:learnings-researcher` which searches both
   `docs/solutions/` and `.factory/learnings.jsonl`
2. `/check` dispatches the same agent when reviewing code that touches previously-documented areas
3. No SessionStart hook is needed — surfacing is pull-based, triggered by skills that need context

## Verification

Before closing, consult `references/verification-checklists.md` for the /compound checklist.

## Anti-Rationalizations

| You might think... | But actually... |
|--------------------|-----------------|
| "I'll document it later" | Context decays in hours. Write it now. |
| "The code is self-documenting" | Code says WHAT, not WHY. Document the decision. |
| "This is too small to document" | Small gotchas compound. 1 line in JSONL takes 10 seconds. |
| "Nobody will read this" | Future /think will auto-surface it. The reader is the machine. |
| "The PR description covers it" | PR descriptions are on GitHub. docs/solutions/ is in the repo. |

## Gotchas

- Wrote a solution doc so long nobody would read it — keep it 20-50 lines
- Forgot YAML frontmatter — doc becomes unsearchable
- Documented the fix but not the WHY — future reader can't judge if it still applies
- Deferred writing to "after the PR" — context was lost, doc was never written
