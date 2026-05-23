# Anti-Rationalization Library

Shared across all skills. When the agent produces any phrase from the left column,
it MUST execute the corresponding counter-action before proceeding.

Sources: superpowers (Iron Laws), agent-skills (per-skill tables), Waza (rationalization watch).

## Universal Rationalizations

| Rationalization | Why It's Wrong | Counter-Action |
|----------------|----------------|----------------|
| "This is a trivial change" | Trivial changes cause production incidents | Run full test suite anyway |
| "I'll add tests later" | Later never comes; untested code ships | Write the test NOW (Iron Law 1) |
| "It should work now" | "Should" is not evidence | Run verification, paste output (Iron Law 3) |
| "Probably fixed" | Probability is not proof | Run the failing scenario again |
| "I'm confident it's X" | Confidence is not evidence | Instrument and measure |
| "This is too simple to need a spec" | Simple requirements breed complex bugs | State the requirement explicitly |
| "The change is obvious" | Obvious to you, not to the reviewer | Document the why |
| "I just need to try one more thing" | No hypothesis, random-walking | State a testable hypothesis first |
| "Probably the same issue as before" | Assumption reuse hides new bugs | Re-investigate from scratch |
| "One more restart should fix it" | Restarts mask root causes | Read the last error body (Iron Law 2) |
| "It works on my machine" | Environment difference IS the bug | Check versions, env vars, config |

## Phase-Specific Rationalizations

### Think Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "Let's just start coding and figure it out" | Enter /think first; zero code until design approved |
| "The requirements are clear enough" | List 3 ambiguities you haven't resolved |
| "We can refactor later" | Design the right abstraction now; refactoring debt compounds |
| "The user already said what they want" | They said WHAT, not WHY, not boundaries, not failure modes; explore first |
| "Asking questions slows us down" | Building the wrong thing is slower; 3 questions now saves 3 hours |
| "This is a small change, no need to think" | Small changes to the wrong thing are still wrong. Enter /think anyway. |
| "I already know the best approach" | Document why in 2 minutes. If you can't, you don't know it. |

### Build Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "This function is too simple to test" | Simple functions compose into complex systems; test it |
| "Mocking is too hard for this" | If it's hard to mock, the coupling is the bug |
| "I'll write integration tests instead of unit tests" | Both; unit tests are faster and more precise |
| "The test infrastructure isn't set up yet" | Set it up. That's part of the task. |
| "TDD slows me down" | TDD slows your typing. It speeds your shipping. |
| "This is just a config change" | Config changes break production. Test the config. |
| "I'll add tests after the PR" | After never comes. RED before GREEN. Always. |
| "The tests pass, so the code is correct" | Tests prove what they test, not what they don't. Check coverage. |
| "I know this works, I tested it manually" | Manual tests evaporate. Automated tests persist. Write it down. |
| "This is a refactor, behavior doesn't change" | Prove it with tests before AND after. Identical results = safe refactor. |
| "The existing code doesn't have tests either" | Not an excuse. Add tests for what you touch. |
| "Worktree setup takes too long" | 3 commands, 10 seconds. Merge conflicts take hours. |

### Check Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "The diff is small, quick review is fine" | Small diffs can contain critical bugs; full review |
| "I wrote it, I know it's correct" | Author blindness is real; let the personas review |
| "The tests pass, so it's fine" | Tests prove what they test, not what they don't |
| "This is just a dependency update" | Dependency updates are a top attack vector. Review the diff. |
| "should work now" | "Should" is a trigger word. Run verification immediately. |

### Hunt Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "That part doesn't matter" | Deflection is a signal; investigate that part |
| "I'll just try this one thing" | State a hypothesis first |
| "It works sometimes" | Intermittent = timing bug; investigate concurrency |
| "I'm confident it's X" | Confidence is not evidence. Instrument and measure. |
| "Probably the same issue as before" | Re-read from scratch. Similar symptoms hide different causes. |
| "One more restart should fix it" | Read the last error. Never restart >2x without new evidence. |

### Plan Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "The plan is in my head" | If you can't write it, you can't execute it |
| "Dependencies are obvious" | Mark them explicitly; context is lost between sessions |
| "I'll figure out file paths during implementation" | Grep now; wrong paths waste entire task cycles |
| "This is too small for a formal plan" | If it has 3+ tasks, it needs a plan. Small plans are fast to write. |
| "Verification commands are obvious" | Write them. Obvious to you, not to the agent in a fresh worktree. |
| "The spec covers everything" | Specs say WHAT. Plans say HOW. Both are needed. |

### Compound Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "I'll document it later" | Context decays in hours; write it now |
| "The code is self-documenting" | Code says WHAT, not WHY; document the decision |
| "Nobody will read this" | Future /think auto-surfaces it; the reader is the machine |
| "This is too small to document" | Small gotchas compound. 1 line in JSONL takes 10 seconds. |
| "The PR description covers it" | PR descriptions live on GitHub. docs/solutions/ is in the repo. Keep it local. |

### Design Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "Clean and modern is a direction" | It's not; name a specific aesthetic |
| "It looks fine to me" | Open a browser; "looks fine" without visual check = fabrication |
| "Cards are the standard pattern" | Standard = template; is there a reason for cards here? |
| "The user didn't specify a style" | That's why you ASK the 4 questions first. |
| "I'll add animations later" | Motion is part of the direction, not decoration. Decide now. |
| "The client won't notice the font" | They'll notice something feels generic. They just won't know why. |

### Ship Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "CI is probably green" | Check it; "probably" is not evidence |
| "This doesn't need a PR" | PRs are the audit trail; create one |
| "The checklist is overkill" | Someone shipped without it and broke prod; that's why it exists |
| "Staging looked fine, no need to check prod" | Staging != production; verify both |
| "I'll clean up worktrees later" | Disk fills up; clean now |

### Health Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "The setup looks fine from memory" | Run data collection; memory is not evidence |
| "Only complex projects need this" | Simple projects benefit most from a clean setup |
| "MCP overhead doesn't matter" | 10% of 200K context is 20K tokens. That's significant. Measure it. |
| "Hooks are working, I can tell" | Run the schema check. Silent failures are the worst kind. |
| "This finding is too minor to report" | Report it as [-] incremental. Let the user decide priority. |

### Retro Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "I remember what I did this week" | You don't; run git log |
| "Seven improvements is better than one" | Zero will get done; pick one |
| "This retro can wait until Monday" | Context decays. Do it now while the week is fresh. |
| "Nothing interesting happened" | Check git log. You forgot something. |
| "The docs/solutions/ are still fine" | When did you last check? Staleness is invisible. |

## Detection

These phrases in agent output trigger mandatory verification:
- "should work now"
- "probably correct"
- "seems to be working"
- "I'm confident"
- "trivial change, no need to verify"
- "this is straightforward"
- "obviously"
