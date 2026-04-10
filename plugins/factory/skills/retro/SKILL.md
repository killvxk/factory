---
name: retro
description: "Weekly retrospective with evidence-based review and knowledge maintenance for the Factory Codex plugin. Use at the end of a week, sprint, or project milestone. Triggers: retro, retrospective, what did we do this week, weekly review, reflect on this sprint."
---

# /factory:retro - Reflect, Clean, Improve

## Goal

Review the week's work using git evidence (not memory), clean up stale knowledge,
and identify one actionable improvement.

## Process

1. **Evidence gathering** (not from memory):
   ```powershell
   git log --oneline --since="7 days ago" --all
   git diff --stat HEAD~20..HEAD  # or appropriate range
   ```

2. **What shipped**: List features, fixes, and changes with commit references.

3. **What was learned**: Review `.factory/learnings.jsonl` entries from the past week:
   ```powershell
   if (Test-Path .factory/learnings.jsonl) {
     $month = Get-Date -Format 'yyyy-MM'
     Get-Content .factory/learnings.jsonl |
       Select-String -Pattern $month |
       Select-Object -Last 20
   }
   ```

4. **Learnings cleanup**: If `.factory/learnings.jsonl` exceeds 200 lines, prune:
   - Remove entries older than 90 days (stale operational tips lose value)
   - Remove duplicates (same learning, different dates)
   - Keep the file under 200 lines
   ```powershell
   if (Test-Path .factory/learnings.jsonl) {
     $lineCount = (Get-Content .factory/learnings.jsonl | Measure-Object -Line).Lines
     $lineCount
     if ($lineCount -gt 200) {
       Get-Content .factory/learnings.jsonl | Select-Object -Last 200 | Set-Content .factory/learnings.jsonl
     }
   }
   ```

5. **Knowledge maintenance**: Review `docs/solutions/` for freshness.
   For each doc, classify:
   - **Keep**: still relevant and accurate
   - **Update**: core is right but details have changed
   - **Delete**: no longer applicable (code was removed/rewritten)

6. **One improvement**: Identify exactly ONE actionable improvement for next week.
   Not three. Not five. One. Make it specific and measurable.

7. **Persist to docs/memory/CODEX.local.md**: Write the retro output to the project-level
   `docs/memory/CODEX.local.md` file so it survives across sessions and influences future Codex behavior.

## Persistence to docs/memory/CODEX.local.md

The retro MUST be written to `docs/memory/CODEX.local.md`.
This file is the project-local Codex memory surface, so retro learnings
automatically influence future sessions in this project.

### Write Strategy

Read `docs/memory/CODEX.local.md` first. If it exists, **update** the `## Retro` section.
If it doesn't exist, create it. Always keep only the LATEST retro
(not a growing history - that's what git log and learnings.jsonl are for).

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

### Why docs/memory/CODEX.local.md

| Alternative | Why not |
|-------------|---------|
| `docs/solutions/` | For solved problems, not weekly status |
| `.factory/learnings.jsonl` | For granular learnings, not the latest local status |
| Conversation only | Gone when session ends |

`docs/memory/CODEX.local.md` is the right place for ephemeral project state like
"what we're focusing on this week" because it stays local to the project while
remaining easy for Codex sessions to load and update.

## Constraints

- Use git log as source of truth, not session memory
- Do not fabricate commit references
- One improvement, not a wish list
- Keep the retro under 5 minutes of reading time
- Always persist to `docs/memory/CODEX.local.md` (do not skip this step)

## Verification

Before closing, confirm the weekly note is current, concise, and consistent with the actual commits and learnings from the week.

## Anti-Rationalizations

| You might think... | But actually... |
|--------------------|-----------------|
| "I remember what I did this week" | You don't. Run git log. Memory is selective. |
| "This retro can wait until Monday" | Context decays. Do it now while the week is fresh. |
| "Nothing interesting happened" | Check git log. You forgot something. |
| "Seven improvements is better than one" | Zero will get done. Pick one. |
| "The docs/solutions/ are still fine" | When did you last check? Staleness is invisible. |

## Gotchas

- Retro based on memory instead of git log - missed half the work
- Listed 7 improvements - none got done. One is enough.
- Forgot to clean stale docs/solutions/ - knowledge rot accumulated
- Retro only in conversation, never written to file - next session started from zero
- Wrote retro to the wrong memory file - next session started from the wrong baseline
