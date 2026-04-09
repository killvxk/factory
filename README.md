# Factory

> v0.4.0

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
/plugin install factory@factory-plugins

# Or clone locally
git clone https://github.com/killvxk/factory ~/.claude/plugins/factory
```

## Skills

Every skill works standalone. Together they form a pipeline.

| Skill | When to use | One-liner |
|-------|------------|-----------|
| `/think` | Before building anything new | Socratic brainstorm with user. Spec review before presenting. Zero code until approved. |
| `/plan` | After direction is confirmed | Zero-ambiguity task list. Mandatory plan spec review. Every task has files, acceptance, verify. |
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

3 human gates + 2 internal quality gates:

**Human gates** (require user approval):
- **G1**: Confirm direction (after /think brainstorm + spec review)
- **G2**: Confirm plan (after /plan + plan spec review)
- **G3**: Confirm ready to ship (after /check multi-persona review)

**Internal quality gates** (automated, no user action):
- **/think spec review**: 4-dimension self-review (completeness, consistency, feasibility, clarity) before presenting design to user
- **/plan spec review**: 4-dimension self-review (completeness, ordering, isolation, verifiability) before presenting plan to user. Deep plans (10+ tasks) dispatch headless document-review.

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
/think reads  <-- factory:learnings-researcher searches (grep)
/check reads  <--/
/retro prunes --> keeps .factory/learnings.jsonl under 200 lines
```

The more you use it, the smarter it gets.

## Agents

| Agent | Role | Dispatched by |
|-------|------|---------------|
| `correctness` | Logic errors, edge cases, state bugs | /check (always) |
| `security` | Injection, auth bypass, secrets | /check (conditional) |
| `adversarial` | Actively constructs failure scenarios | /check (deep reviews) |
| `learnings-researcher` | Searches past solutions via grep | /think, /check |

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
│   └── marketplace.json         # Marketplace registry (source: ./factory)
├── README.md
└── factory/                     # The plugin
    ├── .claude-plugin/
    │   └── plugin.json
    ├── skills/                  # 10 SKILL.md directories
    │   ├── think/
    │   ├── plan/
    │   ├── build/
    │   ├── hunt/
    │   ├── check/
    │   ├── compound/
    │   ├── design/
    │   ├── ship/
    │   ├── health/
    │   └── retro/
    ├── agents/                  # 4 review personas
    │   ├── correctness.md
    │   ├── security.md
    │   ├── adversarial.md
    │   └── learnings-researcher.md
    ├── commands/
    │   └── full-dev.md          # /full-dev pipeline (3 gates)
    ├── references/              # Shared across all skills
    │   ├── iron-laws.md
    │   ├── anti-rationalizations.md
    │   └── verification-checklists.md
    └── docs/
        ├── DESIGN.md
        └── solutions/           # Knowledge store (populated at runtime)
```

## Versioning

Follows [semver](https://semver.org/). Version is tracked in two places:
- `factory/.claude-plugin/plugin.json` (plugin version)
- `.claude-plugin/marketplace.json` (marketplace + plugin entry)

Both MUST stay in sync on every release.

| Version | Date | Summary |
|---------|------|---------|
| 0.4.0 | 2026-04-09 | /retro persists to CLAUDE.local.md; auto-loaded at next session start |
| 0.3.0 | 2026-04-09 | Spec review gates for /think and /plan; interactive brainstorm phase in /think; learnings lifecycle |
| 0.2.0 | 2026-04-08 | Full audit pass: all DNA gaps closed, marketplace structure fixed |
| 0.1.0 | 2026-04-08 | Initial release: 10 skills, 4 agents, 1 pipeline, 3 references |

## License

MIT
