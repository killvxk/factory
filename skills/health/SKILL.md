---
name: health
description: "Audits Claude Code configuration for correctness, security, and effectiveness. Use when Claude ignores instructions, hooks malfunction, MCP needs auditing, or setup feels off. Triggers: health, check my setup, audit config, is my Claude Code setup right, something feels off."
version: 1.0.0
---

# /health — Is Your Setup Working For You?

## Goal

Audit the six layers of Claude Code configuration and report issues by severity.
Optionally compute a weighted 0-10 code quality score.

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

## Gotchas

- `jq` not installed: conversation extraction fails silently — treat as insufficient data
- `python3` not on PATH: MCP checks print "(unavailable)" — don't flag those
- `.next/`, `__pycache__/`, `.turbo/` inflate file counts — exclude generated dirs
- settings.local.json absent is normal for global-settings-only projects
