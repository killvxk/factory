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

## Output

```markdown
## Week of {date}

### Shipped
- {feature/fix}: {1-line description} ({commit ref})

### Learned
- {learning from JSONL or conversation}

### Knowledge Maintenance
- docs/solutions/{file}.md: Keep | Updated | Deleted

### Next Week: One Improvement
- {specific, measurable action}
```

## Constraints

- Use git log as source of truth, not session memory
- Do not fabricate commit references
- One improvement, not a wish list
- Keep the retro under 5 minutes of reading time

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
