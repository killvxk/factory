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

### Build Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "This function is too simple to test" | Simple functions compose into complex systems; test it |
| "Mocking is too hard for this" | If it's hard to mock, the coupling is the bug |
| "I'll write integration tests instead of unit tests" | Both; unit tests are faster and more precise |

### Check Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "The diff is small, quick review is fine" | Small diffs can contain critical bugs; full review |
| "I wrote it, I know it's correct" | Author blindness is real; let the personas review |
| "The tests pass, so it's fine" | Tests prove what they test, not what they don't |

### Hunt Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "That part doesn't matter" | Deflection is a signal; investigate that part |
| "I'll just try this one thing" | State a hypothesis first |
| "It works sometimes" | Intermittent = timing bug; investigate concurrency |

### Plan Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "The plan is in my head" | If you can't write it, you can't execute it |
| "Dependencies are obvious" | Mark them explicitly; context is lost between sessions |
| "I'll figure out file paths during implementation" | Grep now; wrong paths waste entire task cycles |

### Compound Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "I'll document it later" | Context decays in hours; write it now |
| "The code is self-documenting" | Code says WHAT, not WHY; document the decision |
| "Nobody will read this" | Future /think auto-surfaces it; the reader is the machine |

### Design Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "Clean and modern is a direction" | It's not; name a specific aesthetic |
| "It looks fine to me" | Open a browser; "looks fine" without visual check = fabrication |
| "Cards are the standard pattern" | Standard = template; is there a reason for cards here? |

### Ship Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "CI is probably green" | Check it; "probably" is not evidence |
| "This doesn't need a PR" | PRs are the audit trail; create one |
| "The checklist is overkill" | Someone shipped without it and broke prod; that's why it exists |

### Health Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "The setup looks fine from memory" | Run data collection; memory is not evidence |
| "Only complex projects need this" | Simple projects benefit most from a clean setup |

### Retro Phase
| Rationalization | Counter-Action |
|----------------|----------------|
| "I remember what I did this week" | You don't; run git log |
| "Seven improvements is better than one" | Zero will get done; pick one |

## Detection

These phrases in agent output trigger mandatory verification:
- "should work now"
- "probably correct"
- "seems to be working"
- "I'm confident"
- "trivial change, no need to verify"
- "this is straightforward"
- "obviously"
