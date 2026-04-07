---
name: health
description: "Audits Claude Code configuration for correctness, security, and effectiveness. Use when Claude ignores instructions, hooks malfunction, MCP needs auditing, or setup feels off. Triggers: health, check my setup, audit config, is my Claude Code setup right, something feels off."
version: 1.0.0
---

# /health — Is Your Setup Working For You?

## Goal

Audit the six layers of Claude Code configuration and report issues by severity.
Optionally compute a weighted 0-10 code quality score.

## Constraints

- Never fabricate PASS items without evidence (run the actual checks)
- Credentials MUST be redacted before any agent receives data
- If a tool is unavailable (jq, python3), mark that area as [INSUFFICIENT DATA], not a finding
- Do not auto-apply fixes without user confirmation

## Six-Layer Audit

1. **CLAUDE.md** — Does it exist? Is it concise? Any contradictions?
2. **Rules** — Are .claude/rules/ files correctly placed? Any conflicts with CLAUDE.md?
3. **Skills** — Are installed skills functional? Descriptions follow CSO? Token overhead?
4. **Hooks** — hooks.json schema valid? Scripts executable? Hook types correct?
5. **MCP** — Servers responding? Token overhead (>10% of 200K context = flag)?
6. **Behavior** — Recent conversation patterns: does Claude follow instructions?

## Tier Detection

| Tier | Signal | Expected Setup |
|------|--------|----------------|
| Simple | <500 files, 1 contributor, no CI | CLAUDE.md only |
| Standard | 500-5K files, small team or CI | CLAUDE.md + rules + 2-4 skills |
| Complex | >5K files, multi-contributor, CI | Full six-layer setup |

## Data Collection

Single bash block gathers: file count, CLAUDE.md contents, settings.json, hooks.json,
MCP config, skill inventory, startup context estimate (words * 1.3 tokens).

## Dual-Agent Audit (Standard/Complex tiers, from Waza /health)

For Standard and Complex projects, dispatch TWO parallel agents for independent analysis:

**Agent 1 — Context + Security:**
- CLAUDE.md quality and contradictions
- Rules placement and conflicts
- Skill descriptions (CSO compliance)
- MCP token overhead (>10% of 200K context = HIGH, >6 servers = flag)
- Startup context budget (>30K tokens before first message = flag)
- Skill security scan: prompt injection, data exfiltration, destructive commands,
  hardcoded credentials, obfuscation, safety override instructions

**Agent 2 — Control + Behavior:**
- hooks.json schema validation
- allowedTools hygiene (only genuinely dangerous ops)
- Credential exposure in config files
- Model name validation (must follow `claude-*` pattern)
- Three-layer defense consistency (intent/knowledge/enforcement)
- Conversation behavior patterns from recent sessions

For Simple projects, run all checks locally (no subagents needed).

Credentials MUST be redacted before any agent receives data.

## Severity Classification

- **[!] Critical** — Fix now. Claude is actively misbehaving or insecure.
- **[~] Structural** — Fix soon. Setup works but has gaps.
- **[-] Incremental** — Nice to have. Polish items.

## Three-Layer Defense Check

A critical rule should exist in all three layers:
- CLAUDE.md declares intent
- A skill teaches the method
- A hook enforces deterministically

Single-layer rules are flagged as fragile.

## Code Quality Score (Optional)

Weighted 0-10 score across:
- Type checker (25%): `tsc --noEmit` / `cargo check` / `mypy`
- Linter (20%): `eslint` / `clippy` / `ruff`
- Test runner (30%): `npm test` / `cargo test` / `pytest`
- Dead code (15%): unused exports, unreachable branches
- Shell lint (10%): `shellcheck` on any .sh files

## Verification

Before closing, consult `references/verification-checklists.md` for the /health checklist.

## Anti-Rationalizations

| You might think... | But actually... |
|--------------------|-----------------|
| "The setup looks fine from memory" | Run the data collection. Memory is not evidence. |
| "MCP overhead doesn't matter" | 10% of 200K context is 20K tokens — that's significant. |
| "Only Complex projects need this" | Simple projects benefit most — one bad CLAUDE.md wastes every session. |
| "Hooks are working, I can tell" | Can you? Run the schema check. Silent failures are the worst kind. |
| "This finding is too minor to report" | Report it as [-] incremental. Let the user decide. |

## Gotchas

- `jq` not installed: conversation extraction fails silently — treat as insufficient data
- `python3` not on PATH: MCP checks print "(unavailable)" — don't flag those
- `.next/`, `__pycache__/`, `.turbo/` inflate file counts — exclude generated dirs
- settings.local.json absent is normal for global-settings-only projects
