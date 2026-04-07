---
name: hunt
description: "Systematic root-cause debugging. Use when encountering any bug, test failure, crash, unexpected behavior, or when something feels off. Triggers: hunt, debug, fix this bug, why is this failing, investigate, what's wrong. NOT for code review (use /check) or new features (use /build)."
version: 1.0.0
---

# /hunt — Root Cause First, Fix Second

## Goal

Find the root cause of a bug and fix it with evidence. Never guess.
Never patch symptoms. Never touch code until you can state the cause in one sentence.

## Iron Law

```
NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST
```

## Constraints

- Hypothesis must name a specific file, function, line, and condition
- After 3 failed hypotheses: STOP and surface what was checked, ruled out, and unknown
- Fix touches more than 5 files: pause and confirm scope with user
- Same symptom after a fix: stop, re-read execution path from scratch

## Known Failure Shapes

| Shape | Clue | Investigate |
|-------|------|-------------|
| Timing problem | Intermittent, "works sometimes" | Concurrent shared state, race conditions |
| Missing guard | Crash on empty/null/undefined | Optional values, empty collections |
| Ordering bug | "First time works, second doesn't" | Event callbacks, transaction scope |
| Boundary failure | External API errors, timeouts | Service edges, network config |
| Environment mismatch | "Works on my machine" | Env vars, versions, feature flags |
| Stale value | "Shows old data" | In-memory cache, memoized results |
| Condition wait | Flaky in CI, stable locally | Replace fixed sleeps with polling |

## Process

1. **Orient**: Get exact error + stack trace + reproduction steps.
   Run `git log --oneline -20` on involved files. Trace execution backward from symptom.

2. **Hypothesize**: State ONE specific, testable hypothesis.
   - BAD: "A state management issue"
   - GOOD: "Stale cache in useUser at src/hooks/user.ts:42 because dependency array missing userId"

3. **Test**: Instrument or experiment to confirm/reject the hypothesis.
   Never state environment details from memory — run `node --version`, `sw_vers`, etc.

4. **Fix**: Minimum change to address the root cause. Run all tests.

5. **Guard**: Add a regression test that would have caught this bug.

## Freeze Boundary (from gstack /investigate)

When debugging begins, activate a mental freeze boundary:
- Only modify files directly related to the hypothesis
- If you need to edit a file outside the hypothesis scope, STOP and state why
- This prevents "fix-induced regressions" — touching unrelated code while focused on a bug

Practical enforcement:
- Before each edit, check: "Is this file named in my hypothesis?" If no, pause.
- If the fix genuinely requires changes beyond the hypothesis scope, update the hypothesis first.

## Self-Regulation

- Reverted same area twice? Stop and rethink the approach.
- Fix touches >3 files for a single bug? The abstraction might be wrong.
- Three rounds of "fixed but still broken"? The bug is not where you think it is.

## Deflection Signal

"That part doesn't matter" or "That's unrelated" — these areas someone avoids
often contain the actual problem. Investigate them.

## Verification

Before closing, consult `references/verification-checklists.md` for the /hunt checklist.

## Gotchas

- Fixed the wrong code path (client pane vs local pane, guessed from symptom)
- Same symptom, four patches — each buried the real cause deeper
- Stated macOS version from memory, misdiagnosed a notarytool failure
- Tried workarounds before diagnosing MCP tool failure — diagnose first, workaround later
- Wrote the fix before finishing the trace
- Blind restart loop (8 restarts, changing one variable each time, without reading error body)
- Pipeline healthy but no output — needed to test each stage in isolation

## Anti-Rationalizations

| You might think... | But actually... |
|--------------------|-----------------|
| "I'll just try this one thing" | No hypothesis = random walking. State what you expect and why. |
| "I'm confident it's X" | Confidence is not evidence. Instrument and measure. |
| "Probably the same issue as before" | Re-read from scratch. Similar symptoms, different causes. |
| "It works on my machine" | Environment difference IS the bug. |
| "One more restart should fix it" | Read the last error. Never restart >2x without new evidence. |
| "That part doesn't matter" | Deflection is a signal. Look there. |
