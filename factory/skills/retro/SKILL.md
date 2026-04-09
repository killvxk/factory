---
name: retro
description: "Weekly retrospective with evidence-based review and knowledge maintenance. Use at the end of a week, sprint, or project milestone. Triggers: retro, retrospective, what did we do this week, weekly review, reflect on this sprint."
version: 1.0.0
---

# /retro — Reflect, Clean, Improve

## Goal

Review the week's work using git evidence (not memory), clean up stale knowledge,
and identify one actionable improvement.

## Process

1. **Evidence gathering** (not from memory):
   ```bash
   git log --oneline --since="7 days ago" --all
   git diff --stat HEAD~20..HEAD  # or appropriate range
   ```

2. **What shipped**: List features, fixes, and changes with commit references.

3. **What was learned**: Review .factory/learnings.jsonl entries from the past week:
   ```bash
   if [ -f .factory/learnings.jsonl ]; then
     # Show entries from the last 7 days
     grep "$(date +%Y-%m)" .factory/learnings.jsonl | tail -20
   fi
   ```

4. **Learnings cleanup**: If .factory/learnings.jsonl exceeds 200 lines, prune:
   - Remove entries older than 90 days (stale operational tips lose value)
   - Remove duplicates (same learning, different dates)
   - Keep the file under 200 lines
   ```bash
   wc -l .factory/learnings.jsonl 2>/dev/null
   # If >200 lines, keep only last 200:
   # tail -200 .factory/learnings.jsonl > .factory/learnings.tmp && mv .factory/learnings.tmp .factory/learnings.jsonl
   ```

5. **Knowledge maintenance**: Review docs/solutions/ for freshness.
   For each doc, classify:
   - **Keep**: still relevant and accurate
   - **Update**: core is right but details have changed
   - **Delete**: no longer applicable (code was removed/rewritten)

6. **One improvement**: Identify exactly ONE actionable improvement for next week.
   Not three. Not five. One. Make it specific and measurable.

7. **Persist to CLAUDE.local.md**: Write the retro output to the project-level
   `CLAUDE.local.md` file so it survives across sessions and influences future behavior.

## Persistence to CLAUDE.local.md

The retro MUST be written to `CLAUDE.local.md` (project root, gitignored).
This file is loaded by Claude Code at session start, so retro learnings
automatically influence all future sessions in this project.

### Write Strategy

Read `CLAUDE.local.md` first. If it exists, **update** the `## Retro` section.
If it doesn't exist, create it. Always keep only the LATEST retro
(not a growing history — that's what git log and learnings.jsonl are for).

```markdown
# Project Local Notes

## Retro (Week of {date})

### This Week
- {feature}: {1-line} ({commit})
- ...

### Active Improvement
- {the ONE improvement for next week}

### Watch List
- {any recurring pattern from learnings.jsonl worth watching}
```

### Why CLAUDE.local.md

| Alternative | Why not |
|-------------|---------|
| CLAUDE.md | Committed to repo; retro is personal/team-local, not repo-level |
| .factory/retro.md | Not auto-loaded by Claude Code; requires manual read |
| Conversation only | Gone when session ends |
| docs/solutions/ | For solved problems, not weekly status |

`CLAUDE.local.md` is the ONLY file that is both (a) auto-loaded at session start
and (b) gitignored by default. This makes it the right place for ephemeral
project state like "what we're focusing on this week."

## Constraints

- Use git log as source of truth, not session memory
- Do not fabricate commit references
- One improvement, not a wish list
- Keep the retro under 5 minutes of reading time
- Always persist to CLAUDE.local.md (do not skip this step)

## Verification

Before closing, consult `references/verification-checklists.md` for the /retro checklist.

## Anti-Rationalizations

| You might think... | But actually... |
|--------------------|-----------------|
| "I remember what I did this week" | You don't. Run git log. Memory is selective. |
| "This retro can wait until Monday" | Context decays. Do it now while the week is fresh. |
| "Nothing interesting happened" | Check git log. You forgot something. |
| "Seven improvements is better than one" | Zero will get done. Pick one. |
| "The docs/solutions/ are still fine" | When did you last check? Staleness is invisible. |

## Gotchas

- Retro based on memory instead of git log — missed half the work
- Listed 7 improvements — none got done. One is enough.
- Forgot to clean stale docs/solutions/ — knowledge rot accumulated
- Retro only in conversation, never written to file — next session started from zero
- Wrote retro to CLAUDE.md instead of CLAUDE.local.md — polluted shared repo config
