# Factory

> v0.5.0

Factory is a Codex plugin for engineering workflow, review, and knowledge compounding.

It packages:

- 10 skills
- 4 internal reviewer/research personas
- 1 end-to-end pipeline
- 3 iron laws

## Use

Load this repo in Codex and use the namespaced commands:

- `/factory:think` - frame the problem before building
- `/factory:plan` - turn direction into a verifiable plan
- `/factory:hunt` - trace bugs to root cause before fixing
- `/factory:build` - implement with TDD and parallel worktrees
- `/factory:check` - run multi-persona review
- `/factory:compound` - capture solution notes and learnings
- `/factory:design` - shape user-facing interfaces
- `/factory:ship` - verify readiness to merge or deploy
- `/factory:health` - audit Codex setup and plugin wiring
- `/factory:retro` - prune and refresh local memory
- `/factory:full-dev` - run the full think -> plan -> build -> check -> ship -> compound flow

Example:

```text
/factory:full-dev "feature description"
```

There is no Claude marketplace install flow here. The repo is packaged for Codex through `plugins/factory/` and the local marketplace registry at `.agents/plugins/marketplace.json`.

## Plugin Layout

The Codex plugin lives at `plugins/factory/`.

- `plugins/factory/.codex-plugin/plugin.json` defines the plugin metadata and points Codex at the skills bundle.
- `plugins/factory/skills/` contains the 10 workflow skills.
- `plugins/factory/agents/` contains the internal reviewer and research personas.
- `plugins/factory/commands/` contains the command entrypoints, including `full-dev`.
- `plugins/factory/references/` holds the shared policy and verification references.
- `plugins/factory/docs/` contains the design doc and runtime solution notes.

The marketplace registry for this repo is `.agents/plugins/marketplace.json`, which points Codex at the local plugin source.

## Knowledge Loop

Factory keeps its working memory in three places:

- `docs/solutions/` for solution writeups
- `.factory/learnings.jsonl` for compact searchable learnings
- `docs/memory/CODEX.local.md` for local session notes

`/factory:compound` writes into `docs/solutions/` and `.factory/learnings.jsonl`. `/factory:retro` updates `docs/memory/CODEX.local.md`. `/factory:think` and `/factory:check` can surface prior learnings through the research workflow.

## Versioning

Version is tracked in `plugins/factory/.codex-plugin/plugin.json`.

| Version | Date | Summary |
|---------|------|---------|
| 0.5.0 | 2026-04-10 | Codex packaging and plugin metadata migration |
| 0.4.0 | 2026-04-09 | Local memory workflow stabilized before Codex migration |
| 0.3.0 | 2026-04-09 | Spec review gates for /think and /plan; interactive brainstorm phase in /think; learnings lifecycle |
| 0.2.0 | 2026-04-08 | Full audit pass: all DNA gaps closed, marketplace structure fixed |
| 0.1.0 | 2026-04-08 | Initial release: 10 skills, 4 agents, 1 pipeline, 3 references |

## License

MIT
