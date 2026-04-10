# Iron Laws

Three non-negotiable rules enforced across all skills.
Derived from superpowers (Jesse Vincent) empirical testing.

Loaded as a plugin-local Codex reference for skills and review personas.

## Law 1: Test Before Code

```
NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST
```

Write the test. Watch it fail. Only then write the minimum code to pass.
If code was written without a test: DELETE IT and start over.

## Law 2: Root Cause Before Fix

```
NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST
```

State the root cause in one sentence naming file, function, line, and condition.
"A state management issue" is not a root cause.
"Stale cache in useUser at src/hooks/user.ts:42 because dependency array is missing userId" is.

## Law 3: Evidence Before Claims

```
NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE
```

Run the verification command. Read the FULL output. Confirm exit code is 0.
Only then state the claim. Never use "should work", "probably fixed", "seems correct".

## Enforcement

If you catch yourself rationalizing why a Law doesn't apply in this case,
that is the strongest signal that it DOES apply.
