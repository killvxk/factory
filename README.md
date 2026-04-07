# Factory

10 engineering skills. 4 review agents. 1 pipeline. 3 iron laws.

A self-contained Claude Code plugin distilled from five projects:

| Source | What we took |
|--------|-------------|
| [compound-engineering](https://github.com/EveryInc/compound-engineering-plugin) | Knowledge compounding loop, multi-persona review, confidence gating |
| [agent-skills](https://github.com/addyosmani/agent-skills) | Anti-rationalization tables, lifecycle phases, verification checklists |
| [Waza](https://github.com/tw93/waza) | Minimal framing, evidence-based gotchas, trust-the-model writing |
| [superpowers](https://github.com/obra/superpowers) | Iron Laws, persuasion science, CSO, two-stage review |
| [gstack](https://github.com/garrytan/gstack) | Persistent learning, weighted health scoring, CI verification |

## Install

```bash
# From marketplace
/plugin marketplace add killvxk/factory
/plugin install factory@killvxk-factory

# Or clone locally
git clone https://github.com/killvxk/factory ~/.claude/plugins/factory
```

## Skills

Every skill works standalone. Together they form a pipeline.

| Skill | When to use | One-liner |
|-------|------------|-----------|
| `/think` | Before building anything new | Design validation. Checks history. Zero code until approved. |
| `/plan` | After direction is confirmed | Zero-ambiguity task list. Every task has files, acceptance, verify command. |
| `/build` | When implementing features | TDD Iron Law. Red-green-refactor. Parallel worktrees. Two-stage review. |
| `/hunt` | Any bug or unexpected behavior | Root cause first. 7 failure shapes. 3 strikes then stop. |
| `/check` | After implementation, before merge | 3 personas in parallel. Confidence gating. Auto-fix safe issues. |
| `/compound` | After solving a non-trivial problem | Save to docs/solutions/ + learnings.jsonl. Future /think auto-surfaces it. |
| `/design` | Any user-facing interface | Lock direction with 4 questions. Anti-template. Must verify in browser. |
| `/ship` | Ready to merge/deploy | 4 structured options. 6-category checklist. CI must pass. |
| `/health` | Setup feels off or needs auditing | Six-layer audit. Dual-agent for complex projects. Weighted 0-10 score. |
| `/retro` | End of week/sprint | Git-based evidence. Clean stale docs. One improvement, not seven. |

## Pipeline

```
/full-dev "feature description"
```

Chains: `/think` -> `/plan` -> `/build` (TeamCreate parallel TDD) -> `/check` -> `/ship` -> `/compound`

3 human gates:
- **G1**: Confirm direction (after /think)
- **G2**: Confirm plan (after /plan)
- **G3**: Confirm ready to ship (after /check)

Everything else is autonomous.

## Iron Laws

```
1. NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST
2. NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST
3. NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE
```

## Knowledge Loop

```
/compound writes -> docs/solutions/ + .factory/learnings.jsonl
                              |
/think reads  <-- factory:learnings-researcher searches
/check reads  <--/
```

The more you use it, the smarter it gets.

## Agents

| Agent | Role | Dispatched by |
|-------|------|---------------|
| `correctness` | Logic errors, edge cases, state bugs | /check |
| `security` | Injection, auth bypass, secrets | /check (conditional) |
| `adversarial` | Actively constructs failure scenarios | /check (deep reviews) |
| `learnings-researcher` | Searches past solutions | /think, /check |

## Anti-Rationalization

Every skill includes a table of known excuses AI agents use to skip quality steps, with rebuttals. The shared library is at `references/anti-rationalizations.md`.

Example:

| You might think... | But actually... |
|--------------------|-----------------|
| "This is too simple to test" | Simple functions compose into complex systems. Test it. |
| "I'll add tests later" | Later never comes. RED before GREEN. Always. |
| "Should work now" | "Should" is not evidence. Run verification. Paste output. |

## Writing Philosophy

- **Waza style**: Each skill states goals + constraints, not step-by-step procedures. Models improve; minimal framing compounds.
- **Superpowers enforcement**: Iron Laws + anti-rationalization tables prevent shortcuts.
- **Compound memory**: docs/solutions/ + JSONL learnings survive across sessions.
- **Agent Skills organization**: Lifecycle phases (Think -> Plan -> Build -> Check -> Ship -> Compound).
- **CSO**: Skill description fields contain ONLY triggering conditions, never workflow summaries.

## Project Structure

```
.
├── .claude-plugin/
│   └── marketplace.json         # Marketplace registry (points to ./factory)
├── README.md
└── factory/                     # The actual plugin
    ├── .claude-plugin/
    │   └── plugin.json          # Plugin manifest
    ├── skills/
    │   ├── think/SKILL.md
    │   ├── plan/SKILL.md
    │   ├── build/SKILL.md
    │   ├── hunt/SKILL.md
    │   ├── check/SKILL.md
    │   ├── compound/SKILL.md
    │   ├── design/SKILL.md
    │   ├── ship/SKILL.md
    │   ├── health/SKILL.md
    │   └── retro/SKILL.md
    ├── agents/
    │   ├── correctness.md
    │   ├── security.md
    │   ├── adversarial.md
    │   └── learnings-researcher.md
    ├── commands/
    │   └── full-dev.md
    ├── references/
    │   ├── iron-laws.md
    │   ├── anti-rationalizations.md
    │   └── verification-checklists.md
    └── docs/
        ├── DESIGN.md
        └── solutions/           # Knowledge store (populated at runtime)
```

## License

MIT
